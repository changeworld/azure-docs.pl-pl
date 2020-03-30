---
title: Fabryka danych platformy Azure — odwołanie do skryptów JSON
description: Zawiera schematy JSON dla jednostek usługi Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346599"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Fabryka danych platformy Azure — odwołanie do skryptów JSON
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory.


Ten artykuł zawiera schematy JSON i przykłady definiowania jednostek usługi Azure Data Factory (potoku, działania, zestawu danych i połączonej usługi).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Potok
Struktura wysokiego poziomu dla definicji potoku jest następująca:

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

W poniższej tabeli opisano właściwości w definicji JSON potoku:

| Właściwość | Opis | Wymagany
-------- | ----------- | --------
| name | Nazwa potoku. Określ nazwę reprezentującą akcję skonfigurowaną do wykonania działania lub potoku<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się od cyfry\_litery lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">",","","%","&,"":","\\</li></ul> |Tak |
| description |Tekst opisujący, do czego służy działanie lub potok | Nie |
| activities | Zawiera listę działań. | Tak |
| rozpoczynanie |Data rozpoczęcia dla potoku. Musi być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41. <br/><br/>Możliwe jest określenie czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, który jest 6 AM EST.<br/><br/>Właściwości początkowe i końcowe razem określają aktywny okres dla potoku. Wycinki wyjściowe są produkowane tylko w tym aktywnym okresie. |Nie<br/><br/>Jeśli określisz wartość właściwości końcowej, należy określić wartość właściwości start.<br/><br/>Godziny rozpoczęcia i zakończenia mogą być puste, aby utworzyć potok. Należy określić obie wartości, aby ustawić aktywny okres do uruchomienia potoku. Jeśli nie określisz godziny rozpoczęcia i zakończenia podczas tworzenia potoku, można ustawić je przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod później. |
| end |Data-godzina zakończenia dla potoku. Jeśli jest określony musi być w formacie ISO. Na przykład: 2014-10-14T17:32:41 <br/><br/>Możliwe jest określenie czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, który jest 6 AM EST.<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. |Nie <br/><br/>Jeśli określisz wartość właściwości start, należy określić wartość właściwości końcowej.<br/><br/>Zobacz notatki dla właściwości **start.** |
| isPaused (Nieusło) |Jeśli ustawiona na true potoku nie działa. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania. |Nie |
| tryb potoku |Metoda planowania jest uruchamiana dla potoku. Dozwolone wartości to: zaplanowane (domyślnie), jednorazowe.<br/><br/>"Zaplanowane" wskazuje, że potok działa w określonym przedziale czasu zgodnie z jego aktywnym okresem (godzina rozpoczęcia i zakończenia). "Onetime" wskazuje, że potok działa tylko raz. Potoki jednorazowe po utworzeniu nie mogą być modyfikowane/aktualizowane obecnie. Zobacz [potok jednorazowy, aby](data-factory-create-pipelines.md#onetime-pipeline) uzyskać szczegółowe informacje o ustawieniu jednorazowym. |Nie |
| expirationTime |Czas po utworzeniu, dla którego potok jest prawidłowy i powinien pozostać aprowizostał aprowizowany. Jeśli nie ma żadnych aktywnych, nie powiodło się lub oczekujących uruchomień, potok jest usuwany automatycznie po osiągnięciu czasu wygaśnięcia. |Nie |


## <a name="activity"></a>Działanie
Struktura wysokiego poziomu dla działania w ramach definicji potoku (element działania) jest następująca:

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

W poniższej tabeli opisano właściwości w definicji JSON działania:

| Tag | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa działania. Określ nazwę reprezentującą akcję skonfigurowaną do wykonania działania<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się od cyfry\_litery lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">",","","%","&,"":","\\</li></ul> |Tak |
| description |Tekst opisujący, do czego jest używane działanie. |Nie |
| type |Określa typ działania. Zobacz [magazyny danych](#data-stores) i [działania transformacji danych](#data-transformation-activities) dla różnych typów działań. |Tak |
| Wejścia |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nie dla hdinsightstreaming i SqlServerStoredProcedure działania <br/> <br/> Tak dla wszystkich innych |
| Wyjść |Tabele danych wyjściowych używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Tak |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak dla działań HDInsight, działań usługi Azure Machine Learning i działania procedury składowanej. <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji typeProperties zależą od typu działania. |Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określony, używane są domyślne zasady. |Nie |
| scheduler |"scheduler" właściwość służy do definiowania żądanego planowania dla działania. Jego właściwości podrzędne są takie same jak te we [właściwości dostępności we właściwości](data-factory-create-datasets.md#dataset-availability)zestawu danych . |Nie |

### <a name="policies"></a>Zasady
Zasady mają wpływ na zachowanie w czasie wykonywania działania, w szczególności podczas przetwarzania wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| współbieżność |Liczba całkowita <br/><br/>Maksymalna wartość: 10 |1 |Liczba równoczesnych wykonań działania.<br/><br/>Określa liczbę równoległych wykonań działań, które mogą się zdarzyć na różnych plasterkach. Na przykład jeśli działanie musi przejść przez duży zestaw dostępnych danych, o większą wartość współbieżności przyspiesza przetwarzanie danych. |
| wykonaniePriorder |NajnowszeFirst<br/><br/>NajstarszyPierwszość |NajstarszyPierwszość |Określa kolejność wycinków danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz 2 wycinki (jeden dzieje się o 16:00, a drugi o 5pm), a oba są oczekujące wykonanie. Jeśli ustawisz executionPriorityOrder na NewestFirst, plasterek o godzinie 17:00 zostanie przetworzony jako pierwszy. Podobnie jeśli ustawisz executionPriorityORder być OldestFIrst, a następnie plasterek na 4 PM jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może wynosić 10 |0 |Liczba ponownych prób przed przetwarzaniem danych dla plasterka jest oznaczona jako Błąd. Wykonanie działania dla plasterka danych jest ponowione do określonej liczby ponownych prób. Ponowna próby odbywa się tak szybko, jak to możliwe po awarii. |
| timeout |przedział_czasu |00:00:00 |Limit czasu dla działania. Przykład: 00:10:00 (oznacza przesuń czas 10 minut)<br/><br/>Jeśli wartość nie jest określony lub jest 0, limit czasu jest nieskończony.<br/><br/>Jeśli czas przetwarzania danych w plasterku przekracza wartość limitu czasu, jest anulowany, a system próbuje ponowić próbę przetworzenia. Liczba ponownych prób zależy od właściwości ponawiania próby. Po wystąpieniu limitu czasu stan jest ustawiony na TimedOut. |
| Opóźnienie |przedział_czasu |00:00:00 |Określ opóźnienie przed rozpoczęciem przetwarzania danych plasterka.<br/><br/>Wykonywanie działania dla plasterka danych jest uruchamiany po Delay jest przeszłości oczekiwany czas wykonywania.<br/><br/>Przykład: 00:10:00 (oznacza opóźnienie 10 min) |
| longRetry (longRetry) |Liczba całkowita<br/><br/>Maksymalna wartość: 10 |1 |Liczba długich ponownych prób, zanim wykonanie plasterka nie powiedzie się.<br/><br/>longRetry próby są rozmieszczone przez longRetryInterval. Więc jeśli trzeba określić czas między próbami ponawiania, należy użyć longRetry. Jeśli określono zarówno ponów próbę, jak i longRetry, każda próba longRetry zawiera próby ponawiania, a maksymalna liczba prób to Ponów próbę * longRetry.<br/><br/>Na przykład, jeśli w zasadach działania mamy następujące ustawienia:<br/>Ponów próbę: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Załóżmy, że istnieje tylko jeden plasterek do wykonania (stan jest oczekiwanie) i wykonanie działania kończy się niepowodzeniem za każdym razem. Początkowo będą 3 kolejne próby wykonania. Po każdej próbie stan plasterka będzie ponów próbę. Po pierwszych 3 próby są skończą, stan plasterka będzie LongRetry.<br/><br/>Po godzinie (czyli longRetryInteval wartość), nie będzie inny zestaw 3 kolejnych prób wykonania. Po tym stan plasterka będzie niepowodzeniem i nie więcej ponownych prób będzie podejmowana. W związku z tym w sumie podjęto 6 prób.<br/><br/>Jeśli wykonanie zakończy się pomyślnie, stan plasterka będzie gotowy i nie więcej ponownych prób są podejmowane.<br/><br/>longRetry mogą być używane w sytuacjach, gdy dane zależne docierają w czasie niedeterministycznym lub ogólne środowisko jest łuszczące się, w którym następuje przetwarzanie danych. W takich przypadkach robi ponownych prób jeden po drugim może nie pomóc i robi to po interwał czasu powoduje żądane dane wyjściowe.<br/><br/>Słowo ostrzeżenia: nie należy ustawiać wysokie wartości longRetry lub longRetryInterval. Zazwyczaj wyższe wartości oznaczają inne problemy systemowe. |
| longRetryInterval |przedział_czasu |00:00:00 |Opóźnienie między długimi próbami ponowiania prób |

### <a name="typeproperties-section"></a>Sekcja typeProperties
Sekcja typeProperties jest inna dla każdego działania. Działania transformacji mają tylko właściwości typu. Zobacz [działania transformacji danych](#data-transformation-activities) sekcji w tym artykule dla przykładów JSON, które definiują działania transformacji w potoku.

**Działanie kopiowania** ma dwie podsekcje w sekcji typeProperties: **source** i **sink**. Zobacz [sekcję MAGAZYNY DANYCH](#data-stores) w tym artykule dla przykładów JSON, które pokazują, jak używać magazynu danych jako źródła i/lub ujścia.

### <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
W poniższym przykładowym potoku występuje jedno działanie typu **Copy** w sekcji **activities**. W tym przykładzie [działanie kopiowania](data-factory-data-movement-activities.md) kopiuje dane z magazynu obiektów blob platformy Azure do bazy danych SQL platformy Azure.

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

Zobacz [sekcję MAGAZYNY DANYCH](#data-stores) w tym artykule dla przykładów JSON, które pokazują, jak używać magazynu danych jako źródła i/lub ujścia.

Aby uzyskać pełny instruktaż tworzenia tego potoku, zobacz [Samouczek: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Sekcja **definiuje** służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu `${hiveconf:partitionedtable}`gałęzi jako wartości konfiguracji gałęzi (np. `${hiveconf:inputtable}`

Zobacz [działania transformacji danych](#data-transformation-activities) sekcji w tym artykule dla przykładów JSON, które definiują działania transformacji w potoku.

Aby uzyskać pełną instruktaż tworzenia tego potoku, zobacz [Samouczek: Tworzenie pierwszego potoku do przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Połączona usługa
Struktura wysokiego poziomu dla połączonej definicji usługi jest następująca:

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

W poniższej tabeli opisano właściwości w definicji JSON działania:

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| name | Nazwa połączonej usługi. | Tak |
| właściwości - typ | Typ połączonej usługi. Na przykład: Usługa Azure Storage, usługa Azure SQL Database. |
| typeProperties | Sekcja typeProperties zawiera elementy, które są różne dla każdego magazynu danych lub środowiska obliczeniowego. Zobacz sekcję magazyny danych dla wszystkich usług połączonych z magazynem danych i [środowisk obliczeniowych](#compute-environments) dla wszystkich połączonych usług obliczeniowych |

## <a name="dataset"></a>Dataset
Zestaw danych w usłudze Azure Data Factory jest zdefiniowany w następujący sposób:

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

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| name | Nazwa zestawu danych. Zobacz [Usługi Azure Data Factory — reguły nazewnictwa](data-factory-naming-rules.md) dla reguł nazewnictwa. |Tak |Nie dotyczy |
| type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Azure Data Factory (na przykład: AzureBlob, AzureSqlTable). Zobacz sekcję [MAGAZYNY DANYCH](#data-stores) dla wszystkich magazynów danych i typów zestawów danych obsługiwanych przez fabrykę danych. |
| — struktura | Schemat zestawu danych. Zawiera kolumny, ich typy itp. | Nie |Nie dotyczy |
| typeProperties | Właściwości odpowiadające wybranemu typowi. Zobacz [sekcję MAGAZYNY DANYCH](#data-stores) dla obsługiwanych typów i ich właściwości. |Tak |Nie dotyczy |
| external | Flaga logiczna, aby określić, czy zestaw danych jest jawnie produkowany przez potok fabryki danych, czy nie. |Nie |false |
| availability | Definiuje okno przetwarzania lub model krojenia dla produkcji zestawu danych. Aby uzyskać szczegółowe informacje na temat modelu krojenia zestawu danych, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) artykułu. |Tak |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który muszą spełniać wycinki zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz sekcję Zasady zestawu danych. |Nie |Nie dotyczy |

Każda kolumna w sekcji **struktury** zawiera następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| type |Typ danych kolumny.  |Nie |
| kultura |Kultura oparta na sieci .NET ma być `Datetime` używana `Datetimeoffset`podczas określania typu i jest typem .NET lub . Wartość domyślna to `en-us`. |Nie |
| format |Ciąg formatu, który ma być używany, `Datetime` `Datetimeoffset`gdy typ jest określony i jest typem .NET lub . |Nie |

W poniższym przykładzie zestaw danych `slicetimestamp`ma `projectname`trzy `pageviews` kolumny , i są one typu: Ciąg, Ciąg i Dziesiętny odpowiednio.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

W poniższej tabeli opisano właściwości, których można użyć w sekcji **dostępności:**

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu produkcji plasterków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość:</b>Minuta, Godzina, Dzień, Tydzień, Miesiąc |Tak |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" określa, jak często powstaje plasterek.<br/><br/>Jeśli potrzebny jest, aby zestaw danych był krojony co godzinę, należy ustawić <b>częstotliwość</b> na <b>godzinę</b>i <b>interwał</b> na <b>1</b>.<br/><br/><b>Uwaga:</b>Jeśli określisz częstotliwość jako minutę, zalecamy ustawienie interwału na nie mniej niż 15 |Tak |Nie dotyczy |
|  — styl |Określa, czy plasterek powinien być produkowany na początku/na końcu interwału.<ul><li>StartOfInterval (Początekinterwał)</li><li>KoniecInterwał</li></ul><br/><br/>Jeśli częstotliwość jest ustawiona na Miesiąc i styl jest ustawiona na EndOfInterval, plasterek jest produkowany w ostatnim dniu miesiąca. Jeśli styl jest ustawiony na StartOfInterval, plasterek jest produkowany w pierwszym dniu miesiąca.<br/><br/>Jeśli częstotliwość jest ustawiona na Dzień i styl jest ustawiona na EndOfInterval, plasterek jest produkowany w ostatniej godzinie dnia.<br/><br/>Jeśli częstotliwość jest ustawiona na Godzina i styl jest ustawiona na EndOfInterval, plasterek jest produkowany na koniec godziny. Na przykład dla plasterka dla okresu 1 PM - 14:00, plasterek jest produkowany o godzinie 14:00. |Nie |KoniecInterwał |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używane przez harmonogram do obliczania granic plasterków zestawu danych. <br/><br/><b>Uwaga:</b>Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowe niż częstotliwość, to bardziej szczegółowe części są ignorowane. <br/><br/>Na przykład, jeśli <b>interwał</b> jest <b>co godzinę</b> (częstotliwość: godzina i interwał: 1) i <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b> następnie <b>minut i sekund</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Czas, o który są przesuwane początkowe i końcowe wszystkich wycinków zestawu danych. <br/><br/><b>Uwaga:</b>Jeśli określono zarówno anchorDateTime, jak i offset, wynikiem jest połączone przesunięcie. |Nie |Nie dotyczy |

Poniższa sekcja dostępności określa, że wyjściowy zestaw danych jest produkowany co godzinę (lub) wejściowy zestaw danych jest dostępny co godzinę:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Sekcja **zasad** w definicji zestawu danych definiuje kryteria lub warunek, który muszą spełniać wycinki zestawu danych.

| Policy Name (Nazwa zasad) | Opis | Stosowane do | Wymagany | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **obiekcie blob platformy Azure** spełnia minimalne wymagania dotyczące rozmiaru (w megabajtach). |Obiekt bob Azure |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazie danych SQL platformy Azure** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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

Jeśli zestaw danych nie jest produkowany przez usługę Azure Data Factory, powinien być oznaczony jako **zewnętrzny**. To ustawienie zazwyczaj ma zastosowanie do danych wejściowych pierwszego działania w potoku, chyba że używane jest tworzenie łańcucha działania lub potoku.

| Nazwa | Opis | Wymagany | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnić sprawdzenie dostępności danych zewnętrznych dla danego plasterka. Na przykład jeśli dane są dostępne co godzinę, sprawdzanie, aby zobaczyć dane zewnętrzne są dostępne i odpowiedni plasterek jest Gotowy może być opóźnione przy użyciu dataDelay.<br/><br/>Dotyczy tylko chwili obecnej.  Na przykład jeśli jest 13:00 pm teraz i ta wartość jest 10 minut, sprawdzanie poprawności rozpoczyna się o 13:10 PM.<br/><br/>To ustawienie nie ma wpływu na wycinki w przeszłości (plasterki z czasem zakończenia plasterka + dataDelay < Teraz) są przetwarzane bez żadnych opóźnień.<br/><br/>Czas większy niż 23:59 godzin `day.hours:minutes:seconds` należy określić przy użyciu formatu. Na przykład, aby określić 24 godziny, nie należy używać 24:00:00; zamiast tego użyj 1.00:00:00. 24:00:00. Przez 1 dzień i 4 godziny określ 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między niepowodzeniem a następną próbą ponawiania. Jeśli próba nie powiedzie się, następna próba jest po ponawianiuInterval. <br/><br/>Jeśli jest 13:00 pm teraz, zaczynamy pierwszą próbę. Jeśli czas trwania pierwszego sprawdzania poprawności wynosi 1 minutę, a operacja nie powiodła się, następna ponowna próba jest o 1:00 + 1 min (czas trwania) + 1 min (interwał ponawiania) = 1:02 PM. <br/><br/>W przypadku plasterków w przeszłości nie ma opóźnienia. Ponowna próba odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| ponów czas próby |Limit czasu dla każdej próby ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, sprawdzanie poprawności musi zostać zakończone w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, ponawiania czasu.<br/><br/>Jeśli upłynie limit czasu sprawdzania poprawności, plasterek jest oznaczony jako TimedOut. |Nie |00:10:00 (10 minut) |
| maksymalnaRetry |Liczba razy, aby sprawdzić dostępność danych zewnętrznych. Dozwolona maksymalna wartość wynosi 10. |Nie |3 |


## <a name="data-stores"></a>MAGAZYNY DANYCH
Sekcja [Połączone usługi](#linked-service) zawiera opisy elementów JSON, które są wspólne dla wszystkich typów połączonych usług. Ta sekcja zawiera szczegółowe informacje o JSON elementów, które są specyficzne dla każdego magazynu danych.

Sekcja [Zestaw danych](#dataset) zawiera opisy elementów JSON, które są wspólne dla wszystkich typów zestawów danych. Ta sekcja zawiera szczegółowe informacje o JSON elementów, które są specyficzne dla każdego magazynu danych.

W sekcji [Działanie](#activity) podano opisy elementów JSON, które są wspólne dla wszystkich typów działań. Ta sekcja zawiera szczegółowe informacje o JSON elementów, które są specyficzne dla każdego magazynu danych, gdy jest używany jako źródło/zlew w działania kopiowania.

Kliknij łącze do magazynu, który Cię interesuje, aby wyświetlić schematy JSON dla połączonej usługi, zestawu danych i źródła/ujścia dla działania kopiowania.

| Kategoria | Magazyn danych
|:--- |:--- |
| **Azure** |[Magazyn obiektów Blob platformy Azure](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Magazyn danych SQL platformy Azure](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Bazy danych** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[Mysql](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[Mongodb](#mongodb) |
| **Plik** |[Amazon S3](#amazon-s3) |
| &nbsp; |[System plików](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Innych** |[Protokół HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Tabela internetowa |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: usługa połączona usługi Azure Storage i usługa połączona usługi Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć swoje konto magazynu platformy Azure z fabryką danych przy użyciu **klucza konta,** utwórz usługę połączona usługi Azure Storage. Aby zdefiniować usługę połączony usługi Azure Storage, należy ustawić **typ** połączonej usługi na **Platformę AzureStorage**. Następnie można określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z magazynem platformy Azure dla właściwości connectionString. |Tak |

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

#### <a name="azure-storage-sas-linked-service"></a>Usługa połączona usługi Usługi SAS usługi usługi usługi Azure Storage
Usługa połączona usługi Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia fabryce danych ograniczony/czasowy dostęp do wszystkich/określonych zasobów (blob/container) w magazynie. Aby połączyć swoje konto magazynu platformy Azure z fabryką danych przy użyciu usługi Shared Access Signature, utwórz usługę połączoną usługi Azure Storage SAS. Aby zdefiniować usługę połączoną usługi Azure Storage SAS, należy ustawić **typ** połączonej usługi na **Platformę AzureStorageSas**. Następnie można określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| sasuri ( sasuri ) |Określ identyfikator URI podpisu dostępu współdzielonego w zasobach usługi Azure Storage, takich jak obiekt blob, kontener lub tabela. |Tak |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł [łącznika usługi Azure Blob Storage.](data-factory-azure-blob-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych obiektów Blob platformy Azure, należy ustawić **typ** zestawu danych na **AzureBlob**. Następnie określ następujące właściwości specyficzne dla obiektów Blob platformy Azure w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Tak |
| fileName |Nazwa obiektu blob. nazwa pliku jest opcjonalna i rozróżniana wielkość liter.<br/><br/>Jeśli określisz nazwę pliku, działanie (w tym Kopiuj) działa na określonym blob.<br/><br/>Gdy nazwa pliku nie jest określona, Copy zawiera wszystkie obiekty Blob w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego `Data.<Guid>.txt` pliku będzie w następującym formacie: (na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można go użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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


Aby uzyskać więcej informacji, zobacz artykuł [łącznika obiektów Blob platformy Azure.](data-factory-azure-blob-connector.md#dataset-properties)

### <a name="blobsource-in-copy-activity"></a>Obiekt BlobSource w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Blob Storage, ustaw **typ źródła** działania kopiowania na **BlobSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda (wartość domyślna), Fałsz |Nie |

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
Jeśli kopiujesz dane do usługi Azure Blob Storage, ustaw **typ ujścia** działania kopiowania na **BlobSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub FileSystem. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><br/><b>SpłaszczHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><br/><b>MergeFiles (domyślnie):</b> scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu blob jest określona, scalona nazwa pliku będzie określona nazwa; w przeciwnym razie zostanie wygenerowana automatycznie nazwa pliku. |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika obiektów Blob platformy Azure.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone usługi Usługi Azure Data Lake Store, ustaw typ połączonej usługi na **AzureDataLakeStore**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **AzureDataLakeStore** | Tak |
| dataLakeStoreUri | Określ informacje o koncie usługi Azure Data Lake Store. Jest w następującym `https://[accountname].azuredatalakestore.net/webhdfs/v1` formacie: lub `adl://[accountname].azuredatalakestore.net/`. | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do którego należy magazyn Usługi Data Lake Store. | Wymagane do zlewu |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy magazyn Usługi Data Lake Store. | Wymagane do zlewu |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak (w przypadku uwierzytelniania głównego usługi) |
| servicePrincipalKey | Określ klucz aplikacji. | Tak (w przypadku uwierzytelniania głównego usługi) |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak (w przypadku uwierzytelniania głównego usługi) |
| autoryzacja | Kliknij przycisk **Autoryzuj** w **Edytorze fabryki danych** i wprowadź poświadczenia, które przypisuje automatycznie wygenerowany adres URL autoryzacji do tej właściwości. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika)|
| Sessionid | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być użyty tylko raz. To ustawienie jest generowane automatycznie podczas korzystania z Edytora fabryki danych. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika) |

#### <a name="example-using-service-principal-authentication"></a>Przykład: przy użyciu uwierzytelniania jednostkowego usługi
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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Data Lake Store.](data-factory-azure-datalake-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych usługi Azure Data Lake Store, ustaw **typ** zestawu danych na **AzureDataLakeStore**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| folderPath |Ścieżka do kontenera i folderu w magazynie usługi Azure Data Lake. |Tak |
| fileName |Nazwa pliku w magazynie usługi Azure Data Lake. nazwa pliku jest opcjonalna i rozróżniana wielkość liter. <br/><br/>Jeśli określisz nazwę pliku, działanie (w tym Kopiuj) działa na określonym pliku.<br/><br/>Gdy nazwa pliku nie jest określona, Copy zawiera wszystkie pliki w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego `Data.<Guid>.txt` pliku będzie w następującym formacie: (na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można go użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Data Lake Store.](data-factory-azure-datalake-connector.md#dataset-properties)

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Źródło magazynu usługi Azure Data Lake w działaniu kopiowania
Jeśli kopiujesz dane z magazynu usługi Azure Data Lake Store, ustaw **typ źródła** działania kopiowania na **AzureDataLakeStoreSource**i określ następujące właściwości w sekcji **źródłowej:**

**Usługa AzureDataLakeStoreSource** obsługuje następującą sekcję **właściwości typuProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda (wartość domyślna), Fałsz |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Data Lake Store.](data-factory-azure-datalake-connector.md#copy-activity-properties)

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Umywalka magazynu usługi Azure Data Lake w działaniu kopiowania
Jeśli kopiujesz dane do magazynu usługi Azure Data Lake Store, ustaw **typ ujścia** działania kopiowania na **AzureDataLakeStoreSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><br/><b>SpłaszczHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone na pierwszym poziomie folderu docelowego. Pliki docelowe są tworzone z automatycznie wygenerowaną nazwą.<br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu blob jest określona, scalona nazwa pliku będzie określona nazwa; w przeciwnym razie zostanie wygenerowana automatycznie nazwa pliku. |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Data Lake Store.](data-factory-azure-datalake-connector.md#copy-activity-properties)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone usługi Usługi Azure Cosmos DB, ustaw **typ** połączonej usługi na **DocumentDb**i określ następujące właściwości w sekcji **typeProperties:**

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z bazą danych usługi Azure Cosmos DB. |Tak |

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych usługi Azure Cosmos DB, ustaw **typ** zestawu danych na **DocumentDbCollection**i określ następujące właściwości w sekcji **typeProperties:**

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| Collectionname |Nazwa kolekcji usługi Azure Cosmos DB. |Tak |

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#dataset-properties)

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Źródło kolekcji usługi Azure Cosmos DB w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Cosmos DB, ustaw **typ źródła** działania kopiowania na **DocumentDbCollectionSource**i określ następujące właściwości w sekcji **źródłowej:**


| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ kwerendę do odczytu danych. |Ciąg zapytania obsługiwany przez usługę Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Znak specjalny wskazujący, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Usługa Azure Cosmos DB to magazyn NoSQL dla dokumentów JSON, w którym dozwolone są struktury zagnieżdżone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczanie hierarchii za pośrednictwem nestingSeparator, który jest "". w powyższych przykładach. Za pomocą separatora działanie kopiowania wygeneruje obiekt "Name" z trzema elementami podrzędnymi First, Middle i Last, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB Collection Sink in Copy Activity
Jeśli kopiujesz dane do usługi Azure Cosmos DB, ustaw **typ ujścia** działania kopiowania na **DocumentDbCollectionSink**i określ następujące właściwości w sekcji **ujścia:**

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Specjalny znak w nazwie kolumny źródłowej wskazujący, że potrzebny jest zagnieżdżony dokument. <br/><br/>Na przykład `Name.First` powyżej: w tabeli danych wyjściowych tworzy następującą strukturę JSON w dokumencie usługi Cosmos DB:<br/><br/>"Nazwa": {<br/>    "Pierwszy": "John"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure Cosmos DB do tworzenia dokumentów.<br/><br/>Można dostosować wydajność podczas kopiowania danych do/z usługi Azure Cosmos DB przy użyciu tej właściwości. Można oczekiwać lepszej wydajności po zwiększeniu writeBatchSize, ponieważ więcej równoległych żądań do usługi Azure Cosmos DB są wysyłane. Jednak należy unikać ograniczania przepustowości, które mogą rzucać komunikat o błędzie: "Szybkość żądania jest duża".<br/><br/>O ograniczeniu jest wiele czynników, w tym wielkość dokumentów, liczba terminów w dokumentach, zasady indeksowania kolekcji docelowej itp. W przypadku operacji kopiowania można użyć lepszej kolekcji (na przykład S3), aby mieć jak najwięcej dostępnej przepływności (2500 jednostek żądań na sekundę). |Liczba całkowita |Nie (domyślnie: 5) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#copy-activity-properties)

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone usługi Azure SQL Database, ustaw **typ** połączonej usługi na **PlatformĘ AzureSqlDatabase**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z wystąpieniem bazy danych SQL platformy Azure dla właściwości connectionString. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SQL platformy Azure.](data-factory-azure-sql-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych usługi Azure SQL Database, ustaw **typ** zestawu danych na **AzureSqlTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Database, do których odwołuje się usługa połączona. |Tak |

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika SQL platformy Azure.](data-factory-azure-sql-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Działanie źródło SQL w kopiowaniu
Jeśli kopiujesz dane z bazy danych SQL Azure, ustaw **typ źródła** działania kopiowania na **SqlSource**i określ następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika SQL platformy Azure.](data-factory-azure-sql-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Działanie zlewu SQL w działaniu kopiowania
Jeśli kopiujesz dane do bazy danych SQL Azure, ustaw **typ ujścia** działania kopiowania na **SqlSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie, że dane określonego plasterka są czyszczone. |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny działania kopiowania, aby wypełnić automatycznie wygenerowanym identyfikatorem plasterka, który jest używany do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. |Nazwa kolumny z typem danych binarnym(32). |Nie |
| nazwa sqlWriterStoredProcedureName |Nazwa procedury składowanej, która wprowadza dane upsert (aktualizacje/wstawia) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Określ nazwę typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SQL platformy Azure.](data-factory-azure-sql-connector.md#copy-activity-properties)

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona usługi Usługi Azure SQL Data Warehouse, ustaw **typ** połączonej usługi na **AzureSqlDW**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla właściwości connectionString. |Tak |



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

Aby uzyskać więcej informacji, zobacz artykuł łącznika usługi [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych usługi Azure SQL Data Warehouse, ustaw **typ** zestawu danych na **AzureSqlDWTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych usługi Azure SQL Data Warehouse, do których odwołuje się usługa połączona. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika usługi [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)

### <a name="sql-dw-source-in-copy-activity"></a>Sql DW Source w copy activity
Jeśli kopiujesz dane z usługi Azure SQL Data Warehouse, ustaw **typ źródła** działania kopiowania na **SqlDWSource**i określ następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika usługi [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

### <a name="sql-dw-sink-in-copy-activity"></a>Usługa SQL DW Sink w działaniu kopiowania
Jeśli kopiujesz dane do usługi Azure SQL Data Warehouse, ustaw **typ ujścia** działania kopiowania na **SqlDWSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie, że dane określonego plasterka są czyszczone. |Instrukcja kwerendy. |Nie |
| allowPolyBase |Wskazuje, czy ma być używany PolyBase (w stosownych przypadkach) zamiast mechanizmu BULKINSERT. <br/><br/> **Za pomocą PolyBase jest zalecanym sposobem ładowania danych do magazynu danych SQL.** |True <br/>False (domyślnie) |Nie |
| lyBaseSettings (190) |Grupa właściwości, które można określić, gdy właściwość **allowPolybase** jest ustawiona na **true**. |&nbsp; |Nie |
| odrzucanieValue |Określa liczbę lub procent wierszy, które można odrzucić, zanim kwerenda zakończy się niepowodzeniem. <br/><br/>Dowiedz się więcej o opcjach odrzucania bazy PolyBase w sekcji **Argumenty** w temacie [TWORZENIE TABELI ZEWNĘTRZNEJ (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (domyślnie), 1, 2, ... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału czy wartość procentowa. |Wartość (domyślna), Wartość procentowa |Nie |
| odrzucanawłaksza wartość |Określa liczbę wierszy do pobrania, zanim PolyBase ponownie obliczy procent odrzuconych wierszy. |1, 2, ... |Tak, jeśli **rejectType** jest **wartością procentową** |
| użyjTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielonych plikach tekstowych, gdy PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji Argumenty w [programie CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Prawda, fałsz (domyślnie) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika usługi [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone usługi Azure Cognitive Search, ustaw **typ** połączonej usługi na **AzureSearch**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| url | adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora usługi wyszukiwania. | Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cognitive Search.](data-factory-azure-search-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych usługi Azure Cognitive Search, ustaw **typ** zestawu danych na **AzureSearchIndex**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| type | Właściwość typu musi być ustawiona na **AzureSearchIndex**.| Tak |
| nazwa indeksu | Nazwa indeksu wyszukiwania. Fabryka danych nie tworzy indeksu. Indeks musi istnieć w usłudze Azure Cognitive Search. | Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cognitive Search.](data-factory-azure-search-connector.md#dataset-properties)

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Zatonięcie indeksu wyszukiwania funkcji Azure Cognitive Search w aktywności kopiowania
Jeśli kopiujesz dane do indeksu wyszukiwania, ustaw **typ ujścia** działania kopiowania na **AzureSearchIndexSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior (Zachowanie zapisu) | Określa, czy dokument ma być scalany, czy zamieniany, gdy dokument już istnieje w indeksie. | Scalanie (domyślnie)<br/>Upload| Nie |
| Rozmiar writebatchsize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. | od 1 do 1000. Wartość domyślna to 1000. | Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika usługi Azure Cognitive Search.](data-factory-azure-search-connector.md#copy-activity-properties)

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: usługa połączona usługi Azure Storage i usługa połączona usługi Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć swoje konto magazynu platformy Azure z fabryką danych przy użyciu **klucza konta,** utwórz usługę połączona usługi Azure Storage. Aby zdefiniować usługę połączony usługi Azure Storage, należy ustawić **typ** połączonej usługi na **Platformę AzureStorage**. Następnie można określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **AzureStorage** |Tak |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z magazynem platformy Azure dla właściwości connectionString. |Tak |

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

#### <a name="azure-storage-sas-linked-service"></a>Usługa połączona usługi Usługi SAS usługi usługi usługi Azure Storage
Usługa połączona usługi Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia fabryce danych ograniczony/czasowy dostęp do wszystkich/określonych zasobów (blob/container) w magazynie. Aby połączyć swoje konto magazynu platformy Azure z fabryką danych przy użyciu usługi Shared Access Signature, utwórz usługę połączoną usługi Azure Storage SAS. Aby zdefiniować usługę połączoną usługi Azure Storage SAS, należy ustawić **typ** połączonej usługi na **Platformę AzureStorageSas**. Następnie można określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **AzureStorageSas** |Tak |
| sasuri ( sasuri ) |Określ identyfikator URI podpisu dostępu współdzielonego w zasobach usługi Azure Storage, takich jak obiekt blob, kontener lub tabela. |Tak |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł [łącznika usługi Azure Table Storage.](data-factory-azure-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych tabeli platformy Azure, ustaw **typ** zestawu danych na **AzureTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, do których odwołuje się usługa. |Tak. Gdy nazwa tabeli jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli azureTableSourceQuery jest również określony, rekordy z tabeli, która spełnia kwerendy są kopiowane do miejsca docelowego. |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł [łącznika usługi Azure Table Storage.](data-factory-azure-table-connector.md#dataset-properties)

### <a name="azure-table-source-in-copy-activity"></a>Działanie źródło tabel platformy Azure w obszarze Kopiowanie
Jeśli kopiujesz dane z usługi Azure Table Storage, ustaw **typ źródła** działania kopiowania na **AzureTableSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy nazwa tabeli jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli azureTableSourceQuery jest również określony, rekordy z tabeli, która spełnia kwerendy są kopiowane do miejsca docelowego. |
| azureTableSourceIgnoreTableNotFound |Wskazać, czy nie istnieje wyjątek tabeli. |Prawda<br/>FAŁSZ |Nie |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł [łącznika usługi Azure Table Storage.](data-factory-azure-table-connector.md#copy-activity-properties)

### <a name="azure-table-sink-in-copy-activity"></a>Usługa Azure Table Sink w działaniu kopiowania
Jeśli kopiujesz dane do usługi Azure Table Storage, ustaw **typ ujścia** działania kopiowania na **AzureTableSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie określono, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych z pasującymi kluczami partycji i wierszy mają swoje wartości zastąpione lub scalone. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (scalanie i zamienianie), zobacz [Wstawianie lub scalanie tematów encji](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [wstawianie lub zamienianie](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów encji. <br/><br> To ustawienie ma zastosowanie na poziomie wiersza, a nie na poziomie tabeli, a żadna z opcji nie usuwa wierszy w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |scalanie (domyślnie)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout zostanie trafiony. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout zostanie trafiony |Timespan<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślna wartość limitu czasu klienta magazynu 90 s) |

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
Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł [łącznika usługi Azure Table Storage.](data-factory-azure-table-connector.md#copy-activity-properties)

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone z redshift Amazon, ustaw **typ** połączonej usługi na **AmazonRedshift**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Adres IP lub nazwa hosta serwera Amazon Redshift. |Tak |
| port |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwać połączeń klientów. |Nie, wartość domyślna: 5439 |
| database |Nazwa bazy danych Amazon Redshift. |Tak |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych. |Tak |
| hasło |Hasło do konta użytkownika. |Tak |

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

Aby uzyskać więcej informacji, zobacz Amazon Redshift złącza artykułu.

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Amazon Redshift, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Amazon Redshift, do których odwołuje się połączona usługa. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |


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
Aby uzyskać więcej informacji, zobacz Amazon Redshift złącza artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z amazon redshift, ustaw **typ źródła** działania kopiowania na **RelationalSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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
Aby uzyskać więcej informacji, zobacz Amazon Redshift złącza artykułu.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona IBM DB2, ustaw **typ** połączonej usługi **na OnPremisesDB2**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Nazwa serwera DB2. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| Schematu |Nazwa schematu w bazie danych. W nazwie schematu rozróżniana jest wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych DB2. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych DB2. |Tak |

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
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika IBM DB2.

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych DB2, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych DB2, do których odwołuje się usługa. W tableName jest rozróżniana wielkość liter. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)**

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z usługi IBM DB2 należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona MySQL, ustaw **typ** połączonej usługi **na OnPremisesMySql**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Nazwa serwera MySQL. |Tak |
| database |Nazwa bazy danych MySQL. |Tak |
| Schematu |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych MySQL. Możliwe wartości `Basic`to: . |Tak |
| userName |Określ nazwę użytkownika, aby połączyć się z bazą danych MySQL. |Tak |
| hasło |Określ hasło dla określonego konta użytkownika. |Tak |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych MySQL. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika MySQL.](data-factory-onprem-mysql-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych MySQL, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, do których odwołuje się usługa. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika MySQL.](data-factory-onprem-mysql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych MySQL należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |


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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika MySQL.](data-factory-onprem-mysql-connector.md#copy-activity-properties)

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone Oracle, ustaw **typ** połączonej usługi **na OnPremisesOracle**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| typ sterownika | Określ sterownik, który ma być używany do kopiowania danych z/do bazy danych Oracle Database. Dozwolone wartości to **Microsoft** lub **ODP** (domyślnie). Zobacz Obsługiwana wersja i sekcja instalacji na szczegóły sterownika. | Nie |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z wystąpieniem bazy danych Oracle dla właściwości connectionString. | Tak |
| nazwa bramy | Nazwa bramy używanej do łączenia się z lokalnym serwerem Oracle |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [o łączniku Oracle.](data-factory-onprem-oracle-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Oracle, ustaw **typ** zestawu danych na **OracleTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle Database, do których odwołuje się usługa połączona. |Nie (jeśli określono **oracleReaderQuery** **z OracleSource)** |

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
Aby uzyskać więcej informacji, zobacz artykuł [o łączniku Oracle.](data-factory-onprem-oracle-connector.md#dataset-properties)

### <a name="oracle-source-in-copy-activity"></a>Oracle Source w działaniu kopiowania
W przypadku kopiowania danych z bazy danych Oracle ustaw **typ źródła** działania kopiowania na **OracleSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| oracleReaderQuery (wyroczniaReaderQuery) |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable` <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana:`select * from MyTable` |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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

Aby uzyskać więcej informacji, zobacz artykuł [o łączniku Oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

### <a name="oracle-sink-in-copy-activity"></a>Oracle Sink w działaniu kopiowania
W przypadku kopiowania danych do bazy danych am Oracle ustaw **typ zlewu** działania kopiowania na **OracleSink**i określ następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 100) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie, że dane określonego plasterka są czyszczone. |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny działania kopiowania, aby wypełnić automatycznie wygenerowanym identyfikatorem plasterka, który jest używany do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. |Nazwa kolumny z typem danych binarnym(32). |Nie |

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
Aby uzyskać więcej informacji, zobacz artykuł [o łączniku Oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona PostgreSQL, ustaw **typ** połączonej usługi **na OnPremisesPostgreSql**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Nazwa serwera PostgreSQL. |Tak |
| database |Nazwa bazy danych PostgreSQL. |Tak |
| Schematu |Nazwa schematu w bazie danych. W nazwie schematu rozróżniana jest wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych PostgreSQL. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych PostgreSQL. |Tak |

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
Aby uzyskać więcej informacji, zobacz [postgreSql złącza](data-factory-onprem-postgresql-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych PostgreSQL, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, do których odwołuje się usługa. W tableName jest rozróżniana wielkość liter. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

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
Aby uzyskać więcej informacji, zobacz [postgreSql złącza](data-factory-onprem-postgresql-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych PostgreSQL należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: "query": "select * \"from\"MySchema . \"MyTable\"". |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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

Aby uzyskać więcej informacji, zobacz [postgreSql złącza](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artykułu.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona sap business warehouse (BW), należy ustawić **typ** połączonej usługi na **SapBw**i określić następujące właściwości w sekcji **typeProperties:**

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | ciąg | Tak
systemNumer | Numer systemu SAP BW. | Dwucyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak
clientId | Identyfikator klienta klienta w systemie SAP W. | Trzycyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
nazwa bramy | Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym wystąpieniem SAP BW. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

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

Aby uzyskać więcej informacji, zobacz [artykuł łącznika magazynu biznesowego SAP.](data-factory-sap-business-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych SAP BW, należy ustawić **typ** zestawu danych na **RelationalTable**. Nie są obsługiwane żadne właściwości specyficzne dla typu dla zestawu danych SAP BW typu **RelationalTable**.

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
Aby uzyskać więcej informacji, zobacz [artykuł łącznika magazynu biznesowego SAP.](data-factory-sap-business-warehouse-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z magazynu SAP Business Warehouse należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query | Określa kwerendę MDX do odczytu danych z wystąpienia SAP BW. | kwerenda MDX. | Tak |

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

Aby uzyskać więcej informacji, zobacz [artykuł łącznika magazynu biznesowego SAP.](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona SAP HANA, ustaw **typ** połączonej usługi na **SapHana**i określ następujące właściwości w sekcji **typeProperties:**

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa niestandardowego portu, `server:port`określ . | ciąg | Tak
authenticationType | Typ uwierzytelniania. | . "Podstawowe" lub "Windows" | Tak
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
nazwa bramy | Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym wystąpieniem sap hana. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika SAP HANA.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych SAP HANA, należy ustawić **typ** zestawu danych na **RelationalTable**. Nie są obsługiwane żadne właściwości specyficzne dla typu dla zestawu danych SAP HANA typu **RelationalTable**.

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika SAP HANA.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z magazynu danych SAP HANA należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query | Określa kwerendę SQL do odczytu danych z wystąpienia SAP HANA. | Kwerenda SQL. | Tak |


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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SAP HANA.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Połączona usługa
Utworzysz połączaną usługę typu **OnPremisesSqlServer,** aby połączyć lokalną bazę danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla lokalnej usługi połączonej programu SQL Server.

Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej programu SQL Server.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu powinna być ustawiona na: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ connectionString informacje potrzebne do nawiązania połączenia z lokalną bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych programu SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **\\nazwa_domeny .** |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować przy użyciu polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w ciągu połączenia, jak pokazano w poniższym przykładzie **(Właściwości EncryptedCredential):**

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: JSON do używania uwierzytelniania SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: JSON do korzystania z uwierzytelniania systemu Windows

Jeśli nazwa użytkownika i hasło są określone, brama używa ich do personifikacji określonego konta użytkownika, aby połączyć się z lokalną bazą danych programu SQL Server. W przeciwnym razie brama łączy się z programem SQL Server bezpośrednio z kontekstem zabezpieczeń bramy (jej konta startowego).

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika programu SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych programu SQL Server, ustaw **typ** zestawu danych na **SqlServerTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych programu SQL Server, do których odwołuje się usługa połączona. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika programu SQL Server.](data-factory-sqlserver-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Działanie Źródło SQL w kopiowaniu
W przypadku kopiowania danych z bazy danych programu SQL Server należy ustawić **typ źródła** działania kopiowania na **SqlSource**i określić następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. Może odwoływać się do wielu tabel z bazy danych, do których odwołuje się wejściowy zestaw danych. Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: wybierz z MyTable. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla SqlSource, działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych programu SQL Server, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia kwerendy wybierającej do uruchomienia względem bazy danych programu SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Korzystając z **programu sqlReaderStoredProcedureName,** nadal należy określić wartość właściwości **tableName** w zestawie danych JSON. Nie ma jednak żadnych weryfikacji wykonywane dla tej tabeli.


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

W tym przykładzie **sqlReaderQuery** jest określony dla SqlSource. Działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych programu SQL Server, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry). SqlReaderQuery może odwoływać się do wielu tabel w bazie danych, do których odwołuje się wejściowy zestaw danych. Nie jest ograniczona tylko do tabeli ustawionej jako tabela zestawu danychName typeProperty.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia kwerendy wybierającej do uruchomienia względem bazy danych programu SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Aby uzyskać więcej informacji, zobacz artykuł [łącznika programu SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Działanie zlewki sql w kopiowaniu
W przypadku kopiowania danych do bazy danych programu SQL Server należy ustawić **typ zlewu** działania kopiowania na **SqlSink**i określić następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie dane określonego plasterka, że są czyszczone. Aby uzyskać więcej informacji, zobacz sekcję powtarzalności. |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny działania kopiowania, aby wypełnić automatycznie wygenerowanym identyfikatorem plasterka, który jest używany do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz sekcję powtarzalności. |Nazwa kolumny z typem danych binarnym(32). |Nie |
| nazwa sqlWriterStoredProcedureName |Nazwa procedury składowanej, która wprowadza dane upsert (aktualizacje/wstawia) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Określ nazwę typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład
Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z tych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **SqlSink**.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika programu SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona Sybase, ustaw **typ** połączonej usługi **na OnPremisesSybase**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Nazwa serwera Sybase. |Tak |
| database |Nazwa bazy danych Sybase. |Tak |
| Schematu |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych Sybase. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych Sybase. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Sybase.](data-factory-onprem-sybase-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Sybase, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych Sybase, do których odwołuje się usługa. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Sybase.](data-factory-onprem-sybase-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych Sybase ustaw **typ źródła** działania kopiowania na **RelationalSource**i określ następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Sybase.](data-factory-onprem-sybase-connector.md#copy-activity-properties)

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona Teradata, ustaw **typ** połączonej usługi **na OnPremisesTeradata**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Nazwa serwera Teradata. |Tak |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych Teradata. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych Teradata. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Teradata.](data-factory-onprem-teradata-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych obiektu Blob Teradata, należy ustawić **typ** zestawu danych na **RelationalTable**. Obecnie nie są obsługiwane żadne właściwości typu dla zestawu danych Teradata.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Teradata.](data-factory-onprem-teradata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych Teradata, ustaw **typ źródła** działania kopiowania na **RelationalSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Teradata.](data-factory-onprem-teradata-connector.md#copy-activity-properties)

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona cassandra, ustaw **typ** połączonej usługi **na OnPremisesCassandra**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| host |Co najmniej jeden adres IP lub nazwy hostów serwerów Cassandra.<br/><br/>Określ oddzieloną przecinkami listę adresów IP lub nazw hostów, aby połączyć się ze wszystkimi serwerami jednocześnie. |Tak |
| port |Port TCP używany przez serwer Cassandra do nasłuchiwać połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowy lub anonimowy |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| hasło |Określ hasło dla konta użytkownika. |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| nazwa bramy |Nazwa bramy, która jest używana do łączenia się z lokalną bazą danych Cassandra. |Tak |
| encryptedCredential |Poświadczenia zaszyfrowane przez bramę. |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Cassandra.](data-factory-onprem-cassandra-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Cassandra, ustaw **typ** zestawu danych na **CassandraTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Tak (Jeśli **kwerenda** dla **CassandraSource** nie jest zdefiniowana). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (Jeśli **kwerenda** dla **CassandraSource** nie jest zdefiniowana). |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Cassandra.](data-factory-onprem-cassandra-connector.md#dataset-properties)

### <a name="cassandra-source-in-copy-activity"></a>Cassandra Source w copy activity
W przypadku kopiowania danych z cassandra ustaw **typ źródła** działania kopiowania na **CassandraSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Kwerenda SQL-92 lub kwerenda CQL. Zobacz [odwołanie CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Podczas korzystania z kwerendy SQL określ **nazwę name.table obszaru kluczowego,** aby reprezentować tabelę, którą chcesz zbadać. |Nie (jeśli zdefiniowano tableName i keyspace na zestawie danych). |
| spójnośćPoziom |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik danych w celu spełnienia żądania odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKO, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójności danych,](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) aby uzyskać szczegółowe informacje. |Nie. Wartość domyślna to ONE. |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Cassandra.](data-factory-onprem-cassandra-connector.md#copy-activity-properties)

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone mongodb, ustaw **typ** połączonej usługi **na OnPremisesMongoDB**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP używany przez serwer MongoDB do nasłuchiwać połączeń klientów. |Opcjonalna, wartość domyślna: 27017 |
| authenticationType |Podstawowy lub Anonimowy. |Tak |
| nazwa użytkownika |Konto użytkownika, aby uzyskać dostęp do MongoDB. |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| źródło authSource |Nazwa bazy danych MongoDB, której chcesz użyć do sprawdzenia poświadczeń w celu uwierzytelnienia. |Opcjonalne (jeśli używane jest uwierzytelnianie podstawowe). domyślnie: używa konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| Databasename |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| nazwa bramy |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Tak |
| encryptedCredential |Poświadczenia zaszyfrowane przez bramę. |Optional (Opcjonalność) |

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

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych MongoDB, ustaw **typ** zestawu danych na **MongoDbCollection**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Collectionname |Nazwa kolekcji w bazie danych MongoDB. |Tak |

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

#### <a name="mongodb-source-in-copy-activity"></a>Działanie source mongodb w kopiowaniu
Jeśli kopiujesz dane z mongodb, ustaw **typ źródła** działania kopiowania na **MongoDbSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL-92. Na przykład: `select * from MyTable`. |Nie (jeśli określono nazwa zestawu **danych** **collectionName)** |

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
Aby zdefiniować usługę połączone Amazon S3, ustaw **typ** połączonej usługi na **AwsAccessKey**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| identyfikator klucza accessKeyID |Identyfikator tajnego klucza dostępu. |ciąg |Tak |
| klucz tajnyAccess |Sam klucz dostępu tajnego. |Zaszyfrowany tajny ciąg |Tak |

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

Aby uzyskać więcej informacji, zobacz [Artykuł złącza Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Amazon S3, ustaw **typ** zestawu danych na **AmazonS3**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| nazwa łyżki |Nazwa zasobnika S3. |Ciąg |Tak |
| key |Klucz obiektu S3. |Ciąg |Nie |
| Prefiks |Prefiks klucza obiektu S3. Obiekty, których klucze zaczynają się od tego prefiksu są zaznaczone. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| version |Wersja obiektu S3, jeśli jest włączona wersja S3. |Ciąg |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie | |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> bucketName + key określa lokalizację obiektu S3, w którym zasobnik jest kontenerem głównym obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

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
#### <a name="example-sample-data-set-with-version"></a>Przykład: Przykładowy zestaw danych (z wersją)

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

#### <a name="example-dynamic-paths-for-s3"></a>Przykład: Ścieżki dynamiczne dla S3
W przykładzie używamy stałych wartości dla klucza i bucketName właściwości w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Usługa Data Factory może być obliczona dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Można zrobić to samo dla właściwości prefiksu zestawu danych Amazon S3. Zobacz [Funkcje fabryki danych i zmienne systemowe,](data-factory-functions-variables.md) aby uzyskać listę obsługiwanych funkcji i zmiennych.

Aby uzyskać więcej informacji, zobacz [Artykuł złącza Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Działanie źródła systemu plików w kopiowaniu
Jeśli kopiujesz dane z Amazon S3, ustaw **typ źródła** działania kopiowania na **FileSystemSource**i określ następujące właściwości w sekcji **źródłowej:**


| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Określa, czy w katalogu mają być rekursywnie wymieniane obiekty S3. |prawda/fałsz |Nie |


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

Aby uzyskać więcej informacji, zobacz [Artykuł złącza Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>System plików


### <a name="linked-service"></a>Połączona usługa
Lokalny system plików można połączyć z fabryką danych platformy Azure z usługą połączony z **lokalnym serwerem plików.** Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla lokalnej usługi połączonej z serwerem plików.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Upewnij się, że właściwość type jest ustawiona **na OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę główną folderu, który chcesz skopiować. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami. |Tak |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (jeśli wybierzesz encryptedCredential) |
| hasło |Określ hasło użytkownika (identyfikator użytkownika). |Nie (jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (jeśli zdecydujesz się określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| nazwa bramy |Określa nazwę bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym serwerem plików. |Tak |

#### <a name="sample-folder-path-definitions"></a>Przykładowe definicje ścieżek folderów

| Scenariusz | Host w definicji usługi połączonej | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na komputerze bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\podfolder\\* |D:\\ \\ (dla bramy zarządzania danymi 2.0 i nowszych wersji) <br/><br/> localhost (dla starszych wersji niż Brama zarządzania danymi 2.0) |. lub\\\\podfolder folderów (dla bramy zarządzania danymi 2.0 i nowszych wersji) \\ \\ <br/><br/>D:\\ \\ lub\\\\D: podfolder folderów\\\\(dla wersji bramy poniżej 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\podfolder\\ \* \\ \\folderu\\udostępniania\\\\myserver\\lub folderu udostępniania myserver\\* |\\\\\\\\udział myserver\\\\ |. lub podfolder folderu\\\\ \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: używanie nazwy użytkownika i hasła w postaci zwykłego tekstu

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

#### <a name="example-using-encryptedcredential"></a>Przykład: Używanie encryptedcredential

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

Aby uzyskać więcej informacji, zobacz [artykuł łącznika systemu plików](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych systemu plików, należy ustawić **typ** zestawu danych na **FileShare**i określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Określa ścieżkę podrzędną do folderu. Użyj znaku ucieczki '\' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykład 1: "fileFilter": "*.log"<br/>Przykład 2: "fileFilter": 2016-1-?. txt"<br/><br/>Należy zauważyć, że fileFilter ma zastosowanie do wejściowego zestawu danych FileShare. |Nie |
| partitionedBy |Za pomocą partitionedBy można określić dynamiczny folderPath/fileName dla danych szeregów czasowych. Przykładem jest folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**; i obsługiwane poziomy to: **Optymalne** i **najszybsze**. zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można używać fileName i fileFilter jednocześnie.

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

Aby uzyskać więcej informacji, zobacz [artykuł łącznika systemu plików](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Działanie źródła systemu plików w kopiowaniu
W przypadku kopiowania danych z systemu plików należy ustawić **typ źródła** działania kopiowania na **FileSystemSource**i określić następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

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
Aby uzyskać więcej informacji, zobacz [artykuł łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Zatonięcie systemu plików w działaniu kopiowania
W przypadku kopiowania danych do systemu plików należy ustawić **typ ujścia** działania kopiowania na **FileSystemSink**i określić następujące właściwości w sekcji **ujścia:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub FileSystem. |**PreserveHierarchy:** Zachowuje hierarchię plików w folderze docelowym. Oznacza to, że względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak względna ścieżka pliku docelowego do folderu docelowego.<br/><br/>**SpłaszczyćHierarchii:** Wszystkie pliki z folderu źródłowego są tworzone na pierwszym poziomie folderu docelowego. Pliki docelowe są tworzone z automatyczniegenerowaną nazwą.<br/><br/>**Pliki scalania:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/nazwa obiektu blob jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. |Nie |

auto-

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

Aby uzyskać więcej informacji, zobacz [artykuł łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone FTP, ustaw **typ** połączonej usługi na **FtpServer**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| host |Nazwa lub adres IP serwera FTP |Tak |&nbsp; |
| authenticationType |Określanie typu uwierzytelniania |Tak |Podstawowy, Anonimowy |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera FTP |Nie |&nbsp; |
| hasło |Hasło użytkownika (nazwa użytkownika) |Nie |&nbsp; |
| encryptedCredential |Zaszyfrowane poświadczenia dostępu do serwera FTP |Nie |&nbsp; |
| nazwa bramy |Nazwa bramy zarządzania danymi w celu nawiązania połączenia z lokalnym serwerem FTP |Nie |&nbsp; |
| port |Port, na którym nasłuchuje serwer FTP |Nie |21 |
| Enablessl |Określanie, czy protokół FTP ma być używany przez kanał SSL/TLS |Nie |true |
| enableServerCertificateWwadacja |Określanie, czy podczas korzystania z kanału SSL/TLS przy użyciu protokołu FTP za pośrednictwem kanału SSL/TLS należy włączyć sprawdzanie poprawności certyfikatu serwera. |Nie |true |

#### <a name="example-using-anonymous-authentication"></a>Przykład: korzystanie z uwierzytelniania anonimowego

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Przykład: Używanie nazwy użytkownika i hasła w postaci zwykłego tekstu do uwierzytelniania podstawowego

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Przykład: Używanie encryptedCredential do uwierzytelniania i bramy

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika FTP.](data-factory-ftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych FTP, ustaw **typ** zestawu danych na **FileShare**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Podwładna do folderu. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykłady 1:`"fileFilter": "*.log"`<br/>Przykład 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych FileShare. Ta właściwość nie jest obsługiwana przez system PLIKÓW HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia folderu dynamicznegoPath, nazwa pliku dla danych szeregów czasowych. Na przykład folderPath sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**; i obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany jest tryb transferu binarnego. True dla trybu binarnego i fałszywe ASCII. Wartość domyślna: True. Tej właściwości można używać tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwa pliku i fileFilter nie mogą być używane jednocześnie.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika FTP.](data-factory-ftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Działanie źródła systemu plików w kopiowaniu
W przypadku kopiowania danych z serwera FTP należy ustawić **typ źródła** działania kopiowania na **FileSystemSource**i określić następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika FTP.](data-factory-ftp-connector.md#copy-activity-properties)


## <a name="hdfs"></a>SYSTEM PLIKÓW HDFS

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona z usługą HDFS, ustaw **typ** połączonej usługi na **Hdfs**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **Hdfs** |Tak |
| Url |Adres URL do plików TWARDYCH |Tak |
| authenticationType |Anonimowy lub Windows. <br><br> Aby użyć **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z tą sekcją, aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika do uwierzytelniania systemu Windows. |Tak (dla uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. |Tak (dla uwierzytelniania systemu Windows) |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z usługą HDFS. |Tak |
| encryptedCredential |[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) dane wyjściowe poświadczeń dostępu. |Nie |

#### <a name="example-using-anonymous-authentication"></a>Przykład: korzystanie z uwierzytelniania anonimowego

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

#### <a name="example-using-windows-authentication"></a>Przykład: korzystanie z uwierzytelniania systemu Windows

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika HDFS.

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych HDFS, ustaw **typ** zestawu danych na **FileShare**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Na przykład: dla folderu\podfolder, określ podfolder folderów\\\\i\\\\d:\samplefolder, określ d: samplefolder.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt`(na przykład: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia folderu dynamicznegoPath, nazwa pliku dla danych szeregów czasowych. Przykład: folderPath sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> nazwa pliku i fileFilter nie mogą być używane jednocześnie.

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika HDFS.

### <a name="file-system-source-in-copy-activity"></a>Działanie źródła systemu plików w kopiowaniu
W przypadku kopiowania danych z systemu plików HDFS ustaw **typ źródła** działania kopiowania na **FileSystemSource**i określ następujące właściwości w sekcji **źródłowej:**

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł łącznika HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone SFTP, ustaw **typ** połączonej usługi na **Sftp**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Basic**, **SshPublicKey**. <br><br> Zapoznaj się z sekcjami Korzystanie z uwierzytelniania podstawowego i [Korzystanie z klucza publicznego SSH](#using-ssh-public-key-authentication) na większej liczbie właściwości i przykładów JSON. |Tak |
| skipHostKeyValidation (SkipHostKeyValidation) | Określ, czy należy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, `skipHostKeyValidation` jeśli jest ustawiona na false.  |
| nazwa bramy |Nazwa bramy zarządzania danymi w celu nawiązania połączenia z lokalnym serwerem SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Generowane automatycznie po określeniu uwierzytelniania podstawowego (nazwa użytkownika + hasło) lub uwierzytelniania SshPublicKey (nazwa _nazwa + ścieżka klucza prywatnego lub zawartość) w kreatorze kopiowania lub w oknie dialogowym ClickOnce. | Nie. Stosowanie tylko podczas kopiowania danych z lokalnego serwera SFTP. |

#### <a name="example-using-basic-authentication"></a>Przykład: korzystanie z uwierzytelniania podstawowego

Aby użyć uwierzytelniania `authenticationType` `Basic`podstawowego, ustaw jako i określ następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (nazwa użytkownika). | Tak |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: **uwierzytelnianie podstawowe przy użyciu zaszyfrowanych poświadczeń**

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

#### <a name="using-ssh-public-key-authentication"></a>**Korzystanie z uwierzytelniania klucza publicznego SSH:**

Aby użyć uwierzytelniania `authenticationType` `SshPublicKey`podstawowego, ustaw jako i określ następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Tak |
| ścieżka klucza prywatnego | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do który brama może uzyskać dostęp. | Określ `privateKeyPath` albo `privateKeyContent`. <br><br> Stosowanie tylko podczas kopiowania danych z lokalnego serwera SFTP. |
| prywatny KluczWłasny | Szeregowany ciąg zawartości klucza prywatnego. Kreator kopiowania może automatycznie odczytać plik klucza prywatnego i wyodrębnić zawartość klucza prywatnego. Jeśli używasz innego narzędzia/zestawu SDK, należy użyć właściwości privateKeyPath. | Określ `privateKeyPath` albo `privateKeyContent`. |
| Hasło | Określ frazę pass/hasło, aby odszyfrować klucz prywatny, jeśli plik klucza jest chroniony przez frazę przebiegu. | Tak, jeśli plik klucza prywatnego jest chroniony przez frazę przebiegu. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: **uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego**

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SFTP.](data-factory-sftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych SFTP, ustaw **typ** zestawu danych na **FileShare**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Podwładna do folderu. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykłady 1:`"fileFilter": "*.log"`<br/>Przykład 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych FileShare. Ta właściwość nie jest obsługiwana przez system PLIKÓW HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia folderu dynamicznegoPath, nazwa pliku dla danych szeregów czasowych. Na przykład folderPath sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany jest tryb transferu binarnego. True dla trybu binarnego i fałszywe ASCII. Wartość domyślna: True. Tej właściwości można używać tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwa pliku i fileFilter nie mogą być używane jednocześnie.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SFTP.](data-factory-sftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Działanie źródła systemu plików w kopiowaniu
W przypadku kopiowania danych ze źródła SFTP należy ustawić **typ źródła** działania kopiowania na **FileSystemSource**i określić następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |



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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika SFTP.](data-factory-sftp-connector.md#copy-activity-properties)


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone HTTP, ustaw **typ** połączonej usługi na **Http**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| url | Podstawowy adres URL serwera sieci Web | Tak |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **Anonimowy**, **Podstawowy**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Zapoznaj się z sekcjami poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania odpowiednio. | Tak |
| enableServerCertificateWwadacja | Określanie, czy włączyć sprawdzanie poprawności certyfikatu TLS/SSL serwera, jeśli źródłem jest serwer sieci Web HTTPS | Nie, wartość domyślna jest true |
| nazwa bramy | Nazwa bramy zarządzania danymi w celu nawiązania połączenia z lokalnym źródłem HTTP. | Tak, jeśli kopiowanie danych z lokalnego źródła HTTP. |
| encryptedCredential | Zaszyfrowane poświadczenia, aby uzyskać dostęp do punktu końcowego HTTP. Generowane automatycznie podczas konfigurowania informacji uwierzytelniania w kreatorze kopiowania lub w oknie dialogowym ClickOnce. | Nie. Stosowanie tylko podczas kopiowania danych z lokalnego serwera HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Przykład: korzystanie z uwierzytelniania podstawowego, skrótowego lub uwierzytelniania systemu Windows
Ustaw `authenticationType` `Basic`jako `Digest`, `Windows`lub , i określ następujące właściwości oprócz łącznika HTTP ogólne te wprowadzone powyżej:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| nazwa użytkownika | Nazwa użytkownika, aby uzyskać dostęp do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (nazwa użytkownika). | Tak |

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

#### <a name="example-using-clientcertificate-authentication"></a>Przykład: korzystanie z uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania `authenticationType` `ClientCertificate`podstawowego, ustaw jako i określ następujące właściwości oprócz wprowadzonych powyżej właściwości złącza HTTP:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| embeddedCertData | Zakodowana w bazie Base64 zawartość danych binarnych pliku wymiany informacji osobistych (PFX). | Określ `embeddedCertData` albo `certThumbprint`. |
| certThumbprint | Odcisk palca certyfikatu zainstalowanego w magazynie certyfikatów komputera bramy. Stosowanie tylko podczas kopiowania danych z lokalnego źródła HTTP. | Określ `embeddedCertData` albo `certThumbprint`. |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli używasz `certThumbprint` do uwierzytelniania, a certyfikat jest zainstalowany w magazynie osobistym komputera lokalnego, należy udzielić uprawnienia do odczytu do usługi bramy:

1. Uruchom program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty,** która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń pozycję **Certyfikaty**, **Osobiste**i kliknij pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego i wybierz pozycję **Wszystkie zadania**->**Zarządzaj kluczami prywatnymi...**
3. Na karcie **Zabezpieczenia** dodaj konto użytkownika, na którym działa usługa hosta bramy zarządzania danymi z dostępem do odczytu do certyfikatu.

**Przykład: przy użyciu certyfikatu klienta:** Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa certyfikatu klienta zainstalowanego na komputerze z zainstalowaną bramą zarządzania danymi.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Przykład: używanie certyfikatu klienta w pliku
Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa pliku certyfikatu klienta na komputerze z zainstalowaną bramą zarządzania danymi.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika HTTP.](data-factory-http-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych HTTP, ustaw **typ** zestawu danych na **Http**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| względnyUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. <br><br> Aby skonstruować dynamiczny adres URL, można użyć funkcji `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`fabryki danych i [zmiennych systemowych](data-factory-functions-variables.md), Przykład: . | Nie |
| wniosekMetoda | Http metody. Dozwolone wartości to **GET** lub **POST**. | Nie. Wartość domyślna to `GET`. |
| dodatkowePozyty | Dodatkowe nagłówki żądań HTTP. | Nie |
| requestBody (Ciało) | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz po prostu **pobrać dane z punktu końcowego HTTP w stanie takim, w jakim jest** bez analizowania, pomiń te ustawienia formatu. <br><br> Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example-using-the-get-default-method"></a>Przykład: przy użyciu metody GET (domyślna)

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
Aby uzyskać więcej informacji, zobacz artykuł [łącznika HTTP.](data-factory-http-connector.md#dataset-properties)

### <a name="http-source-in-copy-activity"></a>Działanie źródło HTTP w kopiowaniu
Jeśli kopiujesz dane ze źródła HTTP, ustaw **typ źródła** działania kopiowania na **HttpSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (TimeSpan) dla żądania HTTP, aby uzyskać odpowiedź. Jest to limit czasu, aby uzyskać odpowiedź, a nie limit czasu do odczytu danych odpowiedzi. | Nie. Wartość domyślna: 00:01:40 |


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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika HTTP.](data-factory-http-connector.md#copy-activity-properties)

## <a name="odata"></a>OData

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone OData, ustaw **typ** połączonej usługi na **OData**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| url |Adres URL usługi OData. |Tak |
| authenticationType |Typ uwierzytelniania używanego do łączenia się ze źródłem OData. <br/><br/> W przypadku odat w chmurze możliwe wartości to Anonimowe, Podstawowe i OAuth (uwaga Usługa Azure Data Factory obsługuje obecnie tylko usługę Azure Active Directory opartą na OAuth). <br/><br/> W przypadku lokalnego odata możliwe wartości są anonimowe, podstawowe i windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| autoryzowaneCrededential |Jeśli używasz OAuth, kliknij przycisk **Autoryzuj** w Kreatorze kopiowania fabryki danych lub edytorze i wprowadź poświadczenia, a następnie wartość tej właściwości zostanie wygenerowana automatycznie. |Tak (tylko w przypadku korzystania z uwierzytelniania OAuth) |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną usługą OData. Określ tylko, czy są kopiowane dane z lokalnego źródła OData. |Nie |

#### <a name="example---using-basic-authentication"></a>Przykład — używanie uwierzytelniania podstawowego
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

#### <a name="example---using-anonymous-authentication"></a>Przykład — używanie uwierzytelniania anonimowego

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Przykład — przy użyciu uwierzytelniania systemu Windows uzyskiwania dostępu do lokalnego źródła OData

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Przykład — przy użyciu uwierzytelniania OAuth uzyskującego dostęp do źródła OData w chmurze
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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika OData.](data-factory-odata-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych OData, ustaw **typ** zestawu danych na **ODataResource**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika OData.](data-factory-odata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane ze źródła OData, ustaw **typ źródła** działania kopiowania na **RelationalSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Przykład | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |"?$select=Nazwa, Opis&$top=5" |Nie |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika OData.](data-factory-odata-connector.md#copy-activity-properties)


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączoną ODBC, ustaw **typ** połączonej usługi **na OnPremisesOdbc**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Parametry połączenia |Część poświadczeń nieuzyskanych w ciągu połączenia i opcjonalne zaszyfrowane poświadczenia. Zobacz przykłady w poniższych sekcjach. |Tak |
| poświadczenia |Część poświadczeń dostępu ciągu połączenia określona w formacie właściwości-wartość specyficzne dla sterownika. Przykład: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC. Możliwe wartości to: Anonimowy i Podstawowy. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z magazynem danych ODBC. |Tak |

#### <a name="example---using-basic-authentication"></a>Przykład — używanie uwierzytelniania podstawowego

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Przykład — używanie uwierzytelniania podstawowego przy użyciu zaszyfrowanych poświadczeń
Poświadczenia można szyfrować przy użyciu polecenia cmdlet [New-AzDataFactoryEncryptValue.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

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

#### <a name="example-using-anonymous-authentication"></a>Przykład: korzystanie z uwierzytelniania anonimowego

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika ODBC.](data-factory-odbc-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych ODBC, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika ODBC.](data-factory-odbc-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z magazynu danych ODBC należy ustawić **typ źródła** działania kopiowania na **RelationalSource**i określić następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika ODBC.](data-factory-odbc-connector.md#copy-activity-properties)

## <a name="salesforce"></a>SalesForce


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączona Salesforce, ustaw **typ** połączonej usługi na **Salesforce**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| środowiskoUrl | Określ adres URL instancji Salesforce. <br><br> - Domyślnie jest\/to "https: /login.salesforce.com". <br> - Aby skopiować dane zhttps://test.salesforce.compiaskownicy, należy określić " ". <br> - Aby skopiować dane z domeny niestandardowej, należy na przykład określić "https://[domain].my.salesforce.com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| Securitytoken |Określ token zabezpieczający dla konta użytkownika. Zobacz [Pobierz token zabezpieczający,](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) aby uzyskać instrukcje dotyczące resetowania/uzyskania tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokenach zabezpieczających, zobacz [Zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Salesforce.](data-factory-salesforce-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych Salesforce, ustaw **typ** zestawu danych na **RelationalTable**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w salesforce. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Salesforce.](data-factory-salesforce-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z salesforce ustaw **typ źródła** działania kopiowania na **RelationalSource**i określ następujące właściwości w sekcji **źródłowej:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Kwerenda SQL-92 lub [kwerenda języka soql (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Przykład: `select * from MyTable__c`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

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
> "__c" część nazwy interfejsu API jest potrzebna dla dowolnego obiektu niestandardowego.

Aby uzyskać więcej informacji, zobacz artykuł [łącznika Salesforce.](data-factory-salesforce-connector.md#copy-activity-properties)

## <a name="web-data"></a>Dane sieci Web

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługę połączone sieci Web, ustaw **typ** połączonej usługi na **Sieć Web**i określ następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Url |Adres URL do źródła sieci Web |Tak |
| authenticationType |Anonimowy. |Tak |


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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika tabeli sieci Web.](data-factory-web-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Dataset
Aby zdefiniować zestaw danych sieci Web, należy ustawić **typ** zestawu danych na **WebTable**i określić następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |typu zestawu danych. musi być ustawiona na **WebTable** |Tak |
| ścieżka |Względny adres URL do zasobu zawierającego tabelę. |Nie. Jeśli ścieżka nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Aby uzyskać indeks tabeli w sekcji strony HTML, zobacz Uzyskiwanie indeksu tabeli w sekcji html. |Tak |

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika tabeli sieci Web.](data-factory-web-table-connector.md#dataset-properties)

### <a name="web-source-in-copy-activity"></a>Działanie w źródle sieci Web w kopiowaniu
W przypadku kopiowania danych z tabeli sieci Web należy ustawić **typ źródła** działania kopiowania na **WebSource**. Obecnie, gdy źródło w działaniu kopiowania jest typu **WebSource,** nie są obsługiwane żadne dodatkowe właściwości.

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika tabeli sieci Web.](data-factory-web-table-connector.md#copy-activity-properties)

## <a name="compute-environments"></a>ŚRODOWISKA OBLICZENIOWE
W poniższej tabeli wymieniono środowiska obliczeniowe obsługiwane przez fabrykę danych oraz działania transformacji, które można na nich uruchomić. Kliknij łącze dla danych obliczeniowych, które Cię interesują, aby wyświetlić schematy JSON dla połączonej usługi, aby połączyć ją z fabryką danych.

| Środowisko obliczeniowe | Działania |
| --- | --- |
| [Klaster HDInsight na żądanie](#on-demand-azure-hdinsight-cluster) lub [własny klaster HDInsight](#existing-azure-hdinsight-cluster) |[Działanie niestandardowe .NET](#net-custom-activity), [Aktywność hive,](#hdinsight-hive-activity) [Działanie świni,](#hdinsight-pig-activity) [Działanie MapReduce](#hdinsight-mapreduce-activity), Aktywność przesyłania strumieniowego Hadoop, [Aktywność Iskra](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Niestandardowe działanie platformy .NET](#net-custom-activity) |
| [Uczenie maszynowe platformy Azure](#azure-machine-learning) | [Działanie wykonywania wsadowego uczenia maszynowego,](#machine-learning-batch-execution-activity) [działanie aktualizacji zasobu usługi Uczenia maszynowego](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Usługa Azure SQL Database](#azure-sql-database), [usługa Azure SQL Data Warehouse](#azure-sql-data-warehouse), [sql server](#sql-server-stored-procedure) |[Procedura składowana](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Klaster usługi Azure HDInsight na żądanie
Usługa Azure Data Factory może automatycznie utworzyć klaster HDInsight oparty na systemie Windows/Linux na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu (właściwość linkedServiceName w JSON) skojarzone z klastrem. W tej połączonej usłudze można uruchamiać następujące działania transformacji: [działanie niestandardowe .NET](#net-custom-activity), [Działanie gałęzi,](#hdinsight-hive-activity) [Działanie świni,](#hdinsight-pig-activity) [Działanie MapReduce](#hdinsight-mapreduce-activity), aktywność przesyłania strumieniowego Hadoop, [Działanie platformy Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji JSON platformy Azure usługi połączonej HDInsight na żądanie.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość type powinna być ustawiona na **HDInsightOnDemand**. |Tak |
| clusterSize |Liczba węzłów procesu roboczego/danych w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesu roboczego określonych dla tej właściwości. Węzły mają rozmiar Standard_D3 który ma 4 rdzenie, więc klaster węzła roboczego 4 zajmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego, plus 2\*4 = 8 rdzeni dla węzłów głównych). Zobacz [Tworzenie klastrów Hadoop opartych na systemie Linux w hdinsight,](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) aby uzyskać szczegółowe informacje na temat warstwy Standard_D3. |Tak |
| czasochłowe |Dozwolony czas bezczynności dla klastra HDInsight na żądanie. Określa, jak długo klaster HDInsight na żądanie pozostaje aktywny po zakończeniu działania uruchamianego, jeśli w klastrze nie ma żadnych innych aktywnych zadań.<br/><br/>Na przykład jeśli przebieg działania trwa 6 minut i timetolive jest ustawiona na 5 minut, klaster pozostaje przy życiu przez 5 minut po 6 minut przetwarzania przebiegu działania. Jeśli inne uruchomienie działania jest wykonywane z 6 minut okna, jest przetwarzany przez ten sam klaster.<br/><br/>Tworzenie klastra HDInsight na żądanie jest kosztowną operacją (może trochę potrwać), więc użyj tego ustawienia w razie potrzeby, aby poprawić wydajność fabryki danych, ponownie korzystając z klastra HDInsight na żądanie.<br/><br/>Jeśli ustawisz wartość czasową na 0, klaster zostanie usunięty zaraz po uruchomieniu działania w przetwarzaniu. Z drugiej strony, jeśli ustawisz wysoką wartość, klaster może pozostać bezczynny niepotrzebnie powodując wysokie koszty. Dlatego ważne jest, aby ustawić odpowiednią wartość w zależności od potrzeb.<br/><br/>Wiele potoków może współużytkować to samo wystąpienie klastra HDInsight na żądanie, jeśli wartość właściwości czasowej jest odpowiednio ustawiona |Tak |
| version |Wersja klastra HDInsight. Aby uzyskać szczegółowe informacje, zobacz [obsługiwane wersje usługi HDInsight w usłudze Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nie |
| linkedServiceName |Usługa połączona usługi Azure Storage, która ma być używana przez klaster na żądanie do przechowywania i przetwarzania danych. <p>Obecnie nie można utworzyć klastra HDInsight na żądanie, który używa magazynu usługi Azure Data Lake Store. Jeśli chcesz przechowywać dane wynikowe z przetwarzania HDInsight w magazynie usługi Azure Data Lake, użyj działania kopiowania, aby skopiować dane z usługi Azure Blob Storage do magazynu usługi Azure Data Lake.</p>  | Tak |
| additionalLinkedServiceNames |Określa dodatkowe konta magazynu dla usługi połączonej HDInsight, dzięki czemu usługa Data Factory może zarejestrować je w Twoim imieniu. |Nie |
| osType |Typ systemu operacyjnego. Dozwolone wartości to: Windows (domyślnie) i Linux |Nie |
| hcatalogLinkedServiceName |Nazwa usługi połączonej sql platformy Azure, które wskazują na bazę danych HCatalog. Klaster HDInsight na żądanie jest tworzony przy użyciu bazy danych SQL azure jako magazynu metastore. |Nie |

### <a name="json-example"></a>Przykład JSON
Następujący JSON definiuje usługi połączone HDInsight oparte na systemie Linux na żądanie. Usługa Fabryka danych automatycznie tworzy klaster HDInsight **oparty na systemie Linux** podczas przetwarzania plasterka danych.

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

Aby uzyskać więcej informacji, zobacz [artykuł Obliczaj połączone usługi.](data-factory-compute-linked-services.md)

## <a name="existing-azure-hdinsight-cluster"></a>Istniejący klaster usługi Azure HDInsight
Można utworzyć usługę połączona usługi Azure HDInsight, aby zarejestrować własny klaster HDInsight w usłudze Data Factory. W tej połączonej usłudze można uruchamiać następujące działania związane z transformacją danych: [działanie niestandardowe .NET](#net-custom-activity), [działanie gałęzi,](#hdinsight-hive-activity) [Działanie świni,](#hdinsight-pig-activity) [Działanie MapReduce](#hdinsight-mapreduce-activity), aktywność przesyłania strumieniowego Hadoop, [Działanie platformy Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji JSON platformy Azure usługi połączonej usługi Azure HDInsight.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu powinna być ustawiona na **HDInsight**. |Tak |
| clusterUri |Identyfikator URI klastra HDInsight. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, który ma być używany do łączenia się z istniejącym klastrem HDInsight. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| linkedServiceName | Nazwa usługi połączonej usługi Azure Storage, która odwołuje się do magazynu obiektów blob platformy Azure używanego przez klaster HDInsight. <p>Obecnie nie można określić usługi połączonej usługi Usługi Azure Data Lake Store dla tej właściwości. Możesz uzyskać dostęp do danych w magazynie usługi Azure Data Lake ze skryptów Hive/Pig, jeśli klaster HDInsight ma dostęp do magazynu Usługi Data Lake. </p>  |Tak |

W przypadku wersji obsługiwanych klastrów HDInsight zobacz [obsługiwane wersje programu HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

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
Można utworzyć usługę linked Azure Batch, aby zarejestrować pulę partii maszyn wirtualnych (maszyn wirtualnych) w fabryce danych. Działania niestandardowe platformy .NET można uruchamiać przy użyciu usługi Azure Batch lub Usługi Azure HDInsight. [Działanie niestandardowe platformy .NET](#net-custom-activity) można uruchomić w tej połączonej usłudze.

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji usługi Azure JSON usługi połączonej usługi Azure Batch.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu powinna być ustawiona na **AzureBatch**. |Tak |
| accountName |Nazwa konta usługi Azure Batch. |Tak |
| Accesskey |Klucz dostępu dla konta usługi Azure Batch. |Tak |
| nazwa puli |Nazwa puli maszyn wirtualnych. |Tak |
| linkedServiceName |Nazwa połączonej usługi Usługi Azure Storage skojarzonej z tą usługą linked Azure Batch. Ta połączona usługa jest używana do plików przemieszczania wymaganych do uruchomienia działania i przechowywania dzienników wykonywania działań. |Tak |


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
Utwórz usługę połączony usługi Azure Machine Learning, aby zarejestrować punkt końcowy oceniania wsadowego uczenia maszynowego w fabryce danych. Dwa działania przekształcania danych, które można uruchomić w tej połączonej usłudze: [Działanie wykonywania wsadowego uczenia maszynowego,](#machine-learning-batch-execution-activity) [Działanie aktualizacji zasobów uczenia maszynowego](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji usługi Azure JSON usługi połączonej usługi usługi Azure Machine Learning.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Typ |Właściwość typu powinna być ustawiona na: **AzureML**. |Tak |
| mlPunkt |Adres URL oceniania partii. |Tak |
| apiKey (klawisz apiKey) |Interfejs API opublikowanego modelu obszaru roboczego. |Tak |

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
Przed **użyciem** działania U-SQL usługi Usługi Azure Data Lake Analytics w celu połączenia usługi obliczeniowej usługi Azure Data Lake Analytics z fabryką danych platformy Azure przed użyciem [działania U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) w potoku.

### <a name="linked-service"></a>Połączona usługa

Poniższa tabela zawiera opisy właściwości używanych w definicji JSON usługi połączonej usługi Usługi Azure Data Lake Analytics.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Typ |Właściwość typu powinna być ustawiona na: **AzureDataLakeAnalytics**. |Tak |
| accountName |Nazwa konta usługi Azure Data Lake Analytics. |Tak |
| dataLakeAnalyticsUri |Identyfikator URI usługi Azure Data Lake Analytics. |Nie |
| autoryzacja |Kod autoryzacyjny jest automatycznie pobierany po kliknięciu przycisku **Autoryzuj** w Edytorze fabryki danych i wypełnieniu logowania OAuth. |Tak |
| subscriptionId |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określona, używana jest subskrypcja fabryki danych). |
| resourceGroupName |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie określono, używana jest grupa zasobów fabryki danych). |
| Sessionid |identyfikator sesji z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być użyty tylko raz. W przypadku korzystania z Edytora fabryki danych ten identyfikator jest generowany automatycznie. |Tak |


#### <a name="json-example"></a>Przykład JSON
Poniższy przykład zawiera definicję JSON dla usługi połączonej usługi Usługi Azure Data Lake Analytics.

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

## <a name="sql-server-stored-procedure"></a>Procedura składowana programu SQL Server

Utworzysz usługę połączony z programem SQL Server i użyj jej z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) do wywołania procedury składowanej z potoku fabryki danych.

### <a name="linked-service"></a>Połączona usługa
Utworzysz połączaną usługę typu **OnPremisesSqlServer,** aby połączyć lokalną bazę danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla lokalnej usługi połączonej programu SQL Server.

Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej programu SQL Server.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu powinna być ustawiona na: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ connectionString informacje potrzebne do nawiązania połączenia z lokalną bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych programu SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **\\nazwa_domeny .** |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować przy użyciu polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w ciągu połączenia, jak pokazano w poniższym przykładzie **(Właściwości EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: JSON do używania uwierzytelniania SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: JSON do korzystania z uwierzytelniania systemu Windows

Jeśli nazwa użytkownika i hasło są określone, brama używa ich do personifikacji określonego konta użytkownika, aby połączyć się z lokalną bazą danych programu SQL Server. W przeciwnym razie brama łączy się z programem SQL Server bezpośrednio z kontekstem zabezpieczeń bramy (jej konta startowego).

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

Aby uzyskać więcej informacji, zobacz artykuł [łącznika programu SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

## <a name="data-transformation-activities"></a>DZIAŁANIA ZWIĄZANE Z TRANSFORMACJĄ DANYCH

Działanie | Opis
-------- | -----------
[Aktywność gałęzi HDInsight](#hdinsight-hive-activity) | Działanie hive hdinsight w potoku fabryki danych wykonuje zapytania hive na własny lub na żądanie Windows/Linux oparte na klastrze HDInsight.
[Aktywność HDInsight Pig](#hdinsight-pig-activity) | Działanie HIInsight Pig w potoku usługi Data Factory wykonuje zapytania Pig na własny lub na żądanie Windows/ Linux oparte na klastrze HDInsight.
[Mapa HDInsightZredunia aktywności](#hdinsight-mapreduce-activity) | Działanie HDInsight MapReduce w potoku data factory wykonuje programy MapReduce na własną lub na żądanie klaster HDInsight oparty na systemie Windows/Linux.
[Aktywność przesyłania strumieniowego HDInsight](#hdinsight-streaming-activity) | Aktywność przesyłania strumieniowego HDInsight w potoku usługi Data Factory wykonuje programy przesyłania strumieniowego Hadoop na własny lub na żądanie klaster HDInsight oparty na systemie Windows/Linux.
[Aktywność iskrzenie hdinsight](#hdinsight-spark-activity) | Działanie programu HDInsight Spark w potoku usługi Data Factory wykonuje programy Platformy Spark we własnym klastrze HDInsight.
[Działanie wykonywania wsadowego w usłudze Machine Learning](#machine-learning-batch-execution-activity) | Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web usługi Azure Machine Learning do analizy predykcyjnej. Za pomocą działania wykonywania wsadowego w potoku usługi Azure Data Factory, można wywołać usługę sieci web uczenia maszynowego, aby prognoz na danych w partii.
[Działanie aktualizowania zasobów w usłudze Machine Learning](#machine-learning-update-resource-activity) | Z biegiem czasu modele predykcyjne w eksperymentach oceniania uczenia maszynowego muszą zostać przeszkolone przy użyciu nowych wejściowych zestawów danych. Po zakończeniu przekwalifikowania chcesz zaktualizować usługę sieci web oceniania za pomocą ponownie przeszkolonego modelu uczenia maszynowego. Za pomocą działania aktualizuj zasób można zaktualizować usługę sieci web za pomocą nowo przeszkolonego modelu.
[Działania procedur składowanych](#stored-procedure-activity) | Za pomocą działania procedura składowana w potoku fabryki danych można wywołać procedurę składowaną w jednym z następujących magazynów danych: Usługa Azure SQL Database, usługa Azure SQL Data Warehouse, baza danych programu SQL Server w przedsiębiorstwie lub maszyna wirtualna platformy Azure.
[Działanie U-SQL usługi Data Lake Analytics](#data-lake-analytics-u-sql-activity) | Działanie U-SQL usługi Data Lake Analytics uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics.
[Niestandardowe działanie platformy .NET](#net-custom-activity) | Jeśli trzeba przekształcić dane w sposób, który nie jest obsługiwany przez fabrykę danych, można utworzyć działanie niestandardowe z własną logiką przetwarzania danych i użyć działania w potoku. Niestandardowe działanie platformy .NET można skonfigurować do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Działania technologii Hive w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania hive. Właściwość typu działania musi być: **HDInsightHive**. Należy najpierw utworzyć usługę połączona HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na HDInsightHive:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| skrypt |Określanie skryptu gałęzi w linii przewodowej |Nie |
| ścieżka skryptu |Magazyn gałęzi w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości 'script' lub 'scriptPath'. Oba nie mogą być używane razem. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary kluczy i wartości do odwoływania się w skrypcie gałęzi za pomocą 'hiveconf' |Nie |

Te właściwości typu są specyficzne dla działania hive. Inne właściwości (poza typeProperties sekcji) są obsługiwane dla wszystkich działań.

### <a name="json-example"></a>Przykład JSON
Następujące JSON definiuje działania HIVE HDInsight w potoku.

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

Aby uzyskać więcej informacji, zobacz [aktywność gałęzi](data-factory-hive-activity.md) artykułu.

## <a name="hdinsight-pig-activity"></a>Działania technologii Pig w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania świni. Właściwość typu działania musi być: **HDInsightPig**. Należy najpierw utworzyć usługę połączona HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na HDInsightPig:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| skrypt |Określ skrypt Świnia w linii |Nie |
| ścieżka skryptu |Przechowywać skrypt Świergot w magazynie obiektów blob platformy Azure i zapewnić ścieżkę do pliku. Użyj właściwości 'script' lub 'scriptPath'. Oba nie mogą być używane razem. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| Definiuje |Określanie parametrów jako par klucza/wartości do odwoływania się w skrypcie Pig |Nie |

Te właściwości typu są specyficzne dla aktywności świni. Inne właściwości (poza typeProperties sekcji) są obsługiwane dla wszystkich działań.

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

Aby uzyskać więcej informacji, zobacz artykuł Aktywność świni.

## <a name="hdinsight-mapreduce-activity"></a>Działania technologii MapReduce w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania MapReduce. Właściwość typu działania musi być: **HDInsightMapReduce**. Należy najpierw utworzyć usługę połączona HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na HDInsightMapReduce:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| jarLinkedSługa | Nazwa połączonej usługi dla usługi Azure Storage zawierającej plik JAR. | Tak |
| jarFilePath | Ścieżka do pliku JAR w usłudze Azure Storage. | Tak |
| Classname | Nazwa klasy głównej w pliku JAR. | Tak |
| Argumenty | Lista argumentów oddzielonych przecinkami dla programu MapReduce. W czasie wykonywania zostanie wyświetlonych kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty argumentami MapReduce, należy rozważyć użycie zarówno opcji, jak i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s, --input, --output itp., są opcjami bezpośrednio po ich wartościach) | Nie |

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

Aby uzyskać więcej informacji, zobacz [MapReduce activity](data-factory-map-reduce.md) article.

## <a name="hdinsight-streaming-activity"></a>Działania przesyłania strumieniowego usługi HDInsight
Można określić następujące właściwości w definicji JSON działania przesyłania strumieniowego Hadoop. Właściwość typu działania musi być: **HDInsightStreaming**. Należy najpierw utworzyć usługę połączona HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na HDInsightStreaming:

| Właściwość | Opis |
| --- | --- |
| Mapowania | Nazwa pliku wykonywalnego mapera. W tym przykładzie cat.exe jest plikiem wykonywalnym mapera.|
| Reduktor | Nazwa pliku wykonywalnego reduktora. W tym przykładzie wc.exe jest wykonywalnym reduktorem. |
| wejście | Plik wejściowy (w tym lokalizacja) dla mapera. W przykładzie: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample jest kontenerem obiektów blob, example/data/Gutenberg jest folderem, a davinci.txt jest obiektem blob. |
| output | Plik wyjściowy (w tym lokalizacja) reduktora. Dane wyjściowe zadania przesyłania strumieniowego Hadoop jest zapisywany do lokalizacji określonej dla tej właściwości. |
| ścieżka pliku | Ścieżki dla plików wykonywalnych mapera i reduktora. W przykładzie: "adfsample/example/apps/wc.exe", adfsample jest kontenerem obiektów blob, example/apps jest folderem, a wc.exe jest plikiem wykonywalnym. |
| plikLinkedService | Usługa połączona usługi Azure Storage reprezentująca magazyn platformy Azure zawierający pliki określone w sekcji filePaths. |
| Argumenty | Lista argumentów oddzielonych przecinkami dla programu MapReduce. W czasie wykonywania zostanie wyświetlonych kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty argumentami MapReduce, należy rozważyć użycie zarówno opcji, jak i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s, --input, --output itp., są opcjami bezpośrednio po ich wartościach) |
| getDebugInfo | Element opcjonalny. Gdy jest ustawiona na Błąd, dzienniki są pobierane tylko na niepowodzenie. Gdy jest ustawiona na Wszystkie, dzienniki są zawsze pobierane niezależnie od stanu wykonania. |

> [!NOTE]
> Należy określić wyjściowy zestaw danych dla działania przesyłania strumieniowego Hadoop dla właściwości **outputs.** Ten zestaw danych może być tylko fikcyjnym zestawem danych, który jest wymagany do kierowania harmonogramem potoku (co godzinę, codziennie itp.). Jeśli działanie nie przyjmuje danych wejściowych, można pominąć określanie zestawu danych wejściowych dla działania dla właściwości **inputs.**

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

Aby uzyskać więcej informacji, zobacz [Artykuł o aktywności przesyłania strumieniowego Hadoop.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
W definicji JSON działania platformy Spark można określić następujące właściwości. Właściwość typu działania musi być: **HDInsightSpark**. Należy najpierw utworzyć usługę połączona HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na HDInsightSpark:

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| ścieżka rootPath | Kontener i folder obiektów Blob platformy Azure zawierający plik Spark. W nazwie pliku rozróżniana jest wielkość liter. | Tak |
| wpisFilePath | Ścieżka względna do folderu głównego kodu/pakietu Platformy Spark. | Tak |
| Classname | Główna klasa aplikacji Java/Spark | Nie |
| Argumenty | Lista argumentów wiersza polecenia programu Spark. | Nie |
| proxyUżytnik | Konto użytkownika do personifikacji w celu wykonania programu Spark | Nie |
| sparkConfig (niem. | Właściwości konfiguracji platformy Spark. | Nie |
| getDebugInfo | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu platformy Azure używane przez klaster HDInsight (lub) określone przez sparkJobLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie |
| sparkJobLinkedService (SparkJobLinkedService) | Usługa połączona usługi Azure Storage, która przechowuje plik zadania platformy Spark, zależności i dzienniki.  Jeśli nie określisz wartości dla tej właściwości, używany jest magazyn skojarzony z klastrem HDInsight. | Nie |

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

- Właściwość **type** jest ustawiona na **HDInsightSpark**.
- **Ścieżka główna** jest ustawiona na **\\adfspark pyFiles,** gdzie adfspark jest kontenerem obiektów Blob platformy Azure i pyFiles jest poprawnie folder w tym kontenerze. W tym przykładzie usługi Azure Blob Storage jest tym, który jest skojarzony z klastrem Platformy Spark. Możesz przekazać plik do innego usługi Azure Storage. Jeśli to zrobisz, utwórz usługę połączony usługi Azure Storage, aby połączyć to konto magazynu z fabryką danych. Następnie należy określić nazwę połączonej usługi jako wartość właściwości **sparkJobLinkedService.** Zobacz właściwości działania platformy Spark, aby uzyskać szczegółowe informacje na temat tej właściwości i innych właściwości obsługiwanych przez działanie platformy Spark.
- **EntryFilePath** jest ustawiona na **test.py**, który jest plikiem python.
- Właściwość **getDebugInfo** jest ustawiona na **Zawsze**, co oznacza, że pliki dziennika są zawsze generowane (powodzenie lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, aby nie ustawiać tej właściwości na Zawsze w środowisku produkcyjnym, chyba że rozwiązujesz problem.
- Sekcja wyjścia ma jeden **wyjściowy** zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli program iskrzący nie generuje żadnych danych wyjściowych. Wyjściowy zestaw danych napędza harmonogram potoku (co godzinę, codziennie itp.).

Aby uzyskać więcej informacji na temat działania, zobacz [Spark Activity](data-factory-spark.md) artykułu.

## <a name="machine-learning-batch-execution-activity"></a>Działanie wykonywania wsadowego w usłudze Machine Learning
Można określić następujące właściwości w definicji JSON działania wsadowego studia usługi Azure Machine Learning. Właściwość typu działania musi być: **AzureMLBatchExecution**. Należy najpierw utworzyć usługę połączony usługi Azure Machine Learning i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na AzureMLBatchExecution:

Właściwość | Opis | Wymagany
-------- | ----------- | --------
WebServiceInput (WebServiceInput) | Zestaw danych, który ma być przekazywany jako dane wejściowe dla usługi sieci web studia usługi Azure Machine Learning. Ten zestaw danych musi być również uwzględniony w danych wejściowych dla działania. |Użyj webServiceInput lub webServiceInputs. |
WebServiceInputs (WebServiceInputs) | Określ zestawy danych, które mają być przekazywane jako dane wejściowe dla usługi sieci web studio usługi Azure Machine Learning. Jeśli usługa sieci web przyjmuje wiele danych wejściowych, należy użyć właściwości webServiceInputs zamiast używać właściwości webServiceInput. Zestawy danych, do których odwołują się **webServiceInputs,** muszą również być uwzględnione w **danych wejściowych**działania . | Użyj webServiceInput lub webServiceInputs. |
WebServiceOutputs | Zestawy danych, które są przypisane jako dane wyjściowe dla usługi sieci web studio usługi Azure Machine Learning. Usługa sieci web zwraca dane wyjściowe w tym zestawie danych. | Tak |
globalParameters | Określ wartości parametrów usługi sieci web w tej sekcji. | Nie |

### <a name="json-example"></a>Przykład JSON
W tym przykładzie działanie ma zestaw danych **MLSqlInput** jako dane wejściowe i **MLSqlOutput** jako dane wyjściowe. **MlSqlInput** jest przekazywana jako dane wejściowe do usługi sieci web przy użyciu **właściwości webServiceInput** JSON. **MlSqlOutput** jest przekazywana jako dane wyjściowe do usługi sieci Web przy użyciu **webServiceOutputs** właściwości JSON.

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

W przykładzie JSON wdrożona usługa Azure Machine Learning Web używa modułu czytnika i modułu modułu modułu zapisującego do odczytu/zapisu danych z/do bazy danych SQL azure. Ta usługa sieci Web udostępnia następujące cztery parametry: nazwa serwera bazy danych, nazwa bazy danych, nazwa konta użytkownika serwera i hasło konta użytkownika serwera.

> [!NOTE]
> Tylko dane wejściowe i wyjściowe działania AzureMLBatchExecution mogą być przekazywane jako parametry do usługi sieci Web. Na przykład w powyższym fragmentie kodu JSON MLSqlInput jest dane wejściowe do działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pośrednictwem webServiceInput parametru.

## <a name="machine-learning-update-resource-activity"></a>Działanie aktualizowania zasobów w usłudze Machine Learning
Można określić następujące właściwości w usłudze Azure Machine Learning studio Update Resource Activity JSON definicji. Właściwość typu działania musi być: **AzureMLUpdateResource**. Należy najpierw utworzyć usługę połączony usługi Azure Machine Learning i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na AzureMLUpdateResource:

Właściwość | Opis | Wymagany
-------- | ----------- | --------
przeszkolonyModelName | Nazwa przeszkolonego modelu. | Tak |
przeszkolonyModelDatasetName | Zestaw danych wskazujący plik iLearner zwrócony przez operację ponownego trenowania. | Tak |

### <a name="json-example"></a>Przykład JSON
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wsadowe studia azure machine learning przyjmuje dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje szkolenia usługi sieci web (eksperyment szkolenia narażone jako usługa sieci web) z danych szkoleniowych wejściowych i odbiera plik ilearner z usługi sieci web. Symbol zastępczyBlob jest tylko fikcyjny zestaw danych wyjściowych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.


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
Można określić następujące właściwości w definicji JSON działania U-SQL. Właściwość typu działania musi być: **DataLakeAnalyticsU-SQL**. Należy utworzyć usługę połączony usługi Usługi Azure Data Lake Analytics i określić jej nazwę jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na DataLakeAnalyticsU-SQL:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| scriptPath |Ścieżka do folderu zawierającego skrypt U-SQL. W nazwie pliku jest rozróżniana wielkość liter. |Nie (jeśli używasz skryptu) |
| scriptLinkedService |Połączona usługa łącząca magazyn zawierający skrypt z fabryką danych |Nie (jeśli używasz skryptu) |
| skrypt |Określ skrypt wbudowany zamiast określania scriptPath i scriptLinkedService. Na przykład: "script": "CREATE DATABASE test". |Nie (jeśli używasz scriptPath i scriptLinkedService) |
| stopieńParalelizm |Maksymalna liczba węzłów jednocześnie używanych do uruchamiania zadania. |Nie |
| priority |Określa, które zadania ze wszystkich, które są w kolejce, powinny być wybrane do uruchomienia jako pierwsze. Im mniejsza liczba, tym wyższy priorytet. |Nie |
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

Aby uzyskać więcej informacji, zobacz [Data Lake Analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Działania procedur składowanych
Można określić następujące właściwości w definicji JSON działania procedury składowanej. Właściwość typu działania musi być: **SqlServerStoredProcedure**. Należy utworzyć jedną z następujących usług połączonych i określić nazwę połączonej usługi jako wartość właściwości **linkedServiceName:**

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na SqlServerStoredProcedure:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| przechowywaneProcedureName |Określ nazwę procedury składowanej w bazie danych SQL platformy Azure lub usługi Azure SQL Data Warehouse, która jest reprezentowana przez połączone usługi, której używa tabela danych wyjściowych. |Tak |
| przechowywaneParametryprocedure |Określ wartości parametrów procedury składowanej. Jeśli chcesz przekazać null dla parametru, użyj składni: "param1": null (wszystkie małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej na temat korzystania z tej właściwości. |Nie |

Jeśli określisz wejściowy zestaw danych, musi on być dostępny (w stanie "Gotowy") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie może być wykorzystany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. Należy określić wyjściowy zestaw danych dla działania procedury składowanej.

Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc itp.). Wyjściowy zestaw danych musi używać **połączonej usługi,** która odwołuje się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server, w której ma zostać uruchomiony procedura składowana. Wyjściowy zestaw danych może służyć jako sposób przekazywania wyników procedury składowanej do późniejszego przetwarzania przez inne działanie[(działania łańcucha)](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)w potoku. Jednak usługa Data Factory nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje do tabeli SQL, że dane wyjściowe wskazuje na. W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych,** który jest używany tylko do określenia harmonogramu uruchamiania działania procedury składowanej.

### <a name="json-example"></a>Przykład JSON

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

Aby uzyskać więcej informacji, zobacz artykuł [O działanie procedury składowanej.](data-factory-stored-proc-activity.md)

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
W definicji JSON działania niestandardowego platformy .NET można określić następujące właściwości. Właściwość typu działania musi być: **DotNetActivity**. Należy utworzyć usługę połączona usługi Azure HDInsight lub usługę połączona usługi Azure Batch i określić nazwę połączonej usługi jako wartość właściwości **linkedServiceName.** Następujące właściwości są obsługiwane w sekcji **typeProperties** po ustawieniu typu działania na DotNetActivity:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| Assemblyname | Nazwa zestawu. W przykładzie jest: **MyDotnetActivity.dll**. | Tak |
| EntryPoint |Nazwa klasy, która implementuje interfejs IDotNetActivity. W przykładzie jest: **MyDotNetActivityNS.MyDotNetActivity** gdzie MyDotNetActivityNS jest obszar nazw i MyDotNetActivity jest klasą.  | Tak |
| Usługa PackageLinkedService | Nazwa usługi połączonej usługi Azure Storage, która wskazuje magazyn obiektów blob, który zawiera plik zip działania niestandardowego. W przykładzie jest: **AzureStorageLinkedService**.| Tak |
| Plik pakietu | Nazwa pliku zip. W tym przykładzie jest: **customactivitycontainer/MyDotNetActivity.zip**. | Tak |
| Extendedproperties | Rozszerzone właściwości, które można zdefiniować i przekazać do kodu .NET. W tym przykładzie **SliceStart** zmienna jest ustawiona na wartość na podstawie slicestart zmiennej systemowej. | Nie |

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

Aby uzyskać szczegółowe informacje, zobacz [Używanie działań niestandardowych w artykule Data Factory.](data-factory-use-custom-activities.md)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki:

- [Samouczek: tworzenie potoku z działaniem kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Samouczek: tworzenie potoku z działaniem gałęzi](data-factory-build-your-first-pipeline.md)
