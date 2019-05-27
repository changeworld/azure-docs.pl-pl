---
title: Utwórz/harmonogram potoki, działania w usłudze Data Factory połączyć w łańcuch | Dokumentacja firmy Microsoft
description: Dowiedz się utworzyć potok danych w usłudze Azure Data Factory do przenoszenia i przekształcania danych. Utworzyć przepływ w oparciu o dane w celu wygenerowania gotowy do użycia informacje.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ee34c91787ede0431c71b0fd96d2c040717dbca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487422"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Potoki i działania w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-create-pipelines.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [potoków w wersji 2](../concepts-pipelines-activities.md).

Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz z konstruowaniem za ich pomocą pełnych przepływów pracy dla scenariuszy przenoszenia i przetwarzania danych.

> [!NOTE]
> W tym artykule założono, że wykonano już instrukcje [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md). Jeśli nie masz hands-na-doświadczenia z tworzenia fabryk danych, przeprowadzając [samouczkiem dotyczącym przekształcania danych](data-factory-build-your-first-pipeline.md) i/lub [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) może pomóc lepiej zrozumieć, w tym artykule.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd
Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Możesz na przykład użyć działania kopiowania w celu skopiowania danych z lokalnego programu SQL Server do usługi Azure Blob Storage. Następnie użyj działania usługi Hive, które uruchamia skrypt Hive w klastrze usługi Apache HDInsight w celu przetworzenia/przekształcenia danych z magazynu obiektów blob, aby utworzyć dane wyjściowe. Na koniec użyj drugiego działania kopiowania w celu skopiowania danych wyjściowych do usługi Microsoft Azure SQL Data Warehouse, na podstawie której tworzone są rozwiązania raportowania analizy biznesowej (BI).

Dane działanie może — ale nie musi — korzystać z wejściowych [zestawów danych](data-factory-create-datasets.md) i generować co najmniej jeden wyjściowy [zestaw danych](data-factory-create-datasets.md). Na poniższym diagramie przedstawiono relację między potokiem, działaniem i zestawem danych w usłudze Data Factory:

![Relacja potoku, działania i zestaw danych](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Potok umożliwia zarządzania działaniami jako zestawem, nie każdego z nich osobno. Na przykład można wdrożyć, zaplanować, zawieszać i wznowić potoku, zamiast rozwiązywania problemów związanych z działaniami w potoku niezależnie.

Usługa Data Factory obsługuje dwa typy działań — w zakresie przekształcania oraz przenoszenia danych. Każde działanie może mieć zero lub więcej danych wejściowych [zestawów danych](data-factory-create-datasets.md) i tworzące co najmniej jeden wyjściowe zestawy danych.

Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Po utworzeniu zestawu danych można go użyć z działaniami w potoku. Na przykład zestaw danych może być zestawem danych wejściowych/wyjściowych działania kopiowania lub działania HDInsightHive. Aby uzyskać więcej informacji na temat zestawów danych, zobacz artykuł [Datasets in Azure Data Factory](data-factory-create-datasets.md) (Zestawy danych w usłudze Azure Data Factory).

### <a name="data-movement-activities"></a>Działania dotyczące przenoszenia danych
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

Aby uzyskać więcej informacji, zobacz artykuł [Działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Działania dotyczące przekształcania danych
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Aby uzyskać więcej informacji, zobacz artykuł [Działania dotyczące przekształcania danych](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Niestandardowe działania programu .NET
Jeśli musisz przenieść dane do/z danych magazynu, że działanie kopiowania nie obsługuje, lub przekształcania danych za pomocą własnej logiki, Utwórz **niestandardowe działanie platformy .NET**. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

## <a name="schedule-pipelines"></a>Potoki harmonogramu
Potok jest aktywny tylko między jego **start** czasu i **zakończenia** czasu. Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymana, nie są wykonywane niezależnie od czasu rozpoczęcia i zakończenia. Dla potoku do uruchamiania go powinna nie można wstrzymać. Zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) zrozumienie sposobu planowania i wykonywania działania usługi Azure Data Factory.

## <a name="pipeline-json"></a>Format JSON potoku
Przyjrzyjmy się bliżej definicji potoku w formacie JSON. Ogólna struktura potoku wygląda następująco:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa potoku. Określ nazwę, która reprezentuje akcję wykonywaną przez potok. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się literą, cyfrą lub znakiem podkreślenia (\_)</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<",">", "\*", "%", "&", ":","\\"</li></ul> |Yes |
| description | Wprowadź tekst opisujący przeznaczenie potoku. |Yes |
| activities | W sekcji **activities** można zdefiniować jedno lub więcej działań. Zobacz następną sekcję, aby uzyskać szczegółowe informacje na temat elementu JSON activities. | Yes |
| rozpoczynanie | Data i godzina rozpoczęcia dla potoku. Musi znajdować się w [ISO format](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: `2016-10-14T16:32:41Z`. <br/><br/>Istnieje możliwość określenia czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00-05:00`", który jest szacowany AM 6<br/><br/>Właściwości początkowe i końcowe razem określają aktywny okres potoku. Wycinki danych wyjściowych tylko są tworzone za pomocą w tym okresie active. |Nie<br/><br/>Jeśli określisz wartości dla właściwości end, należy określić wartość dla właściwości rozpoczęcia.<br/><br/>Czas rozpoczęcia i zakończenia zarówno można pozostawić puste, aby utworzyć potok. Należy określić zarówno wartości można ustawić okresu aktywności potoku do uruchomienia. Jeśli nie określono godziny rozpoczęcia i zakończenia podczas tworzenia potoku, można ustawić je później przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| end | Data / Godzina zakończenia dla potoku. Jeśli zostanie określony, musi być w formacie ISO. Na przykład: `2016-10-14T17:32:41Z` <br/><br/>Istnieje możliwość określenia czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00-05:00`, które jest szacowane AM 6<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. <br/><br/> Potok jest aktywny tylko między jego czas rozpoczęcia i zakończenia. Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymana, nie są wykonywane niezależnie od czasu rozpoczęcia i zakończenia. Dla potoku do uruchamiania go powinna nie można wstrzymać. Zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) zrozumienie sposobu planowania i wykonywania działania usługi Azure Data Factory. |Nie <br/><br/>Jeśli określisz wartości dla właściwości uruchamiania, należy określić wartość dla właściwości end.<br/><br/>Zobacz informacje o **start** właściwości. |
| isPaused | Jeśli ustawiono wartość true, potok nie jest uruchamiany. Jest ona w stanie wstrzymania. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania potoku. |Nie |
| pipelineMode | Metoda Planowanie uruchomienia potoku. Dozwolone wartości to: (ustawienie domyślne), zaplanowane jednorazowa.<br/><br/>"Regularne" wskazuje, że potok jest uruchamiany w określonych odstępach czasu zgodnie z jego aktywny okres (czas rozpoczęcia i zakończenia). "Jednorazowe" wskazuje, że potok jest uruchamiany tylko raz. Jednorazowa potoki po utworzeniu nie może być zmodyfikowane/zaktualizowane obecnie. Zobacz [potoku Onetime](#onetime-pipeline) Aby uzyskać szczegółowe informacje o ustawieniach jednorazowa. |Nie |
| expirationTime | Czas, po utworzeniu, dla którego [potoku jednorazowego](#onetime-pipeline) jest prawidłowa i powinny pozostać elastycznie. Jeśli nie ma żadnych aktywnych, nie powiodło się, lub do czasu uruchomienia potoku jest automatycznie usunięte po osiągnięciu czas wygaśnięcia. Wartość domyślna: `"expirationTime": "3.00:00:00"`|Nie |
| datasets |Lista zestawów danych, który będzie używany przez działania definiowane w potoku. Ta właściwość może służyć do definiowania zestawów danych, które są specyficzne dla tego potoku i nie jest zdefiniowany w fabryce danych. Zestawy danych zdefiniowanych w tym potoku można używać tylko w ramach tego potoku i nie może być współużytkowana. Zobacz [zakresu zestawów danych](data-factory-create-datasets.md#scoped-datasets) Aby uzyskać szczegółowe informacje. |Nie |

## <a name="activity-json"></a>Format JSON działania
W sekcji **activities** można zdefiniować jedno lub więcej działań. Każde działanie ma następującą strukturę najwyższego poziomu:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

Poniższa tabela zawiera opis właściwości w definicji JSON działania:

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name | Nazwa działania. Określ nazwę, która reprezentuje akcję wykonywaną przez działanie. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się literą, cyfrą lub znakiem podkreślenia (\_)</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Yes |
| description | Tekst opisujący przeznaczenie działania |Yes |
| type | Typ działania. Zobacz [działania przenoszenia danych](#data-movement-activities) i [działania przekształcania danych](#data-transformation-activities) sekcje dla różnych typów działań. |Yes |
| dane wejściowe |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Yes |
| wyjścia |Dane wyjściowe tabele używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Yes |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak dla działań HDInsight i Azure Machine Learning działanie wsadowego oceniania przez <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w **typeProperties** sekcji zależą od typu działania. Aby wyświetlić właściwości typu dla działania, kliknij linki do działań w poprzedniej sekcji. | Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określona, używane są domyślne zasady. |Nie |
| Harmonogram | Właściwość "harmonogram" jest używana do definiowania żądanego planowania dla działania. Właściwości podrzędnych są takie same, jak te w [właściwości availability w zestawie danych](data-factory-create-datasets.md#dataset-availability). |Nie |

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

## <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
W poniższym przykładowym potoku występuje jedno działanie typu **Copy** w sekcji **activities**. W tym przykładzie [działanie copy](data-factory-data-movement-activities.md) kopiuje dane z usługi Azure Blob Storage do bazy danych Azure SQL Database.

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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**.
* Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. Definiowanie zestawów danych w formacie JSON opisano w artykule [Zestawy danych](data-factory-create-datasets.md).
* W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. W [działania przenoszenia danych](#data-movement-activities) sekcji, kliknij magazyn danych, że chcesz użyć jako źródła lub ujścia, aby dowiedzieć się więcej na temat przenoszenia danych do i z tego magazynu danych.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczka: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: kopiowanie danych z usługi Blob Storage do usługi SQL Database).

## <a name="sample-transformation-pipeline"></a>Przykładowy potok przekształcania
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** został ustawiony na wartość **HDInsightHive**.
* Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie usługi Azure Storage (określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService**) oraz w folderze **script** w kontenerze **adfgetstarted**.
* `defines` Sekcja jest używana do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu hive jako wartości konfiguracyjne magazynu Hive (np. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Sekcja **typeProperties** jest inna dla każdego działania przekształcania. Aby dowiedzieć się więcej na temat właściwości typu obsługiwanych dla działania przekształcania, kliknij działanie przekształcania w [działania przekształcania danych](#data-transformation-activities) tabeli.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczka: Tworzenie pierwszego potoku do przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Poprzednie dwa przykładowe potoki zawierają tylko po jednym działaniu. Potok może obejmować więcej niż jedno działanie.

Jeśli masz wiele działań w potoku, a dane wyjściowe działania nie jest dane wejściowe kolejnego działania, działania mogą być wykonywane równolegle, jeśli wycinki danych wejściowych dla działania jest gotowe.

Dwa działania można połączyć w łańcuch dzięki wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Drugie działanie wykonuje, tylko gdy pierwsza z nich zakończy się pomyślnie.

![Tworzenie łańcuchów działań w tym samym potoku](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

W tym przykładzie ten potok ma dwa działania: Działania Activity1 i Activity2. Działania Activity1 przyjmuje Dataset1 jako dane wejściowe i wyjściowe Dataset2. Działanie przyjmuje Dataset2 jako dane wejściowe i wyjściowe Dataset3. Ponieważ dane wyjściowe działania Activity1 (Dataset2) jest w danych wejściowych Activity2 i uruchamia Activity2 tylko wtedy, gdy działanie zakończy się pomyślnie i utworzenie wycinka Dataset2. Jeśli działania Activity1 nie powiedzie się z jakiegoś powodu, nie generuje wycinek Dataset2 2 działania nie działa dla tego wycinka (na przykład: 9 AM celu 10 AM).

Ponadto można połączyć w łańcuch działań, które znajdują się w różnych potoków.

![Tworzenie łańcuchów działań w dwa potoki](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

W tym przykładzie Pipeline1 ma tylko jedno działanie, która przyjmuje Dataset1 jako dane wejściowe i generuje Dataset2 jako dane wyjściowe. Pipeline2 również ma tylko jedno działanie, która przyjmuje Dataset2 jako dane wejściowe i Dataset3 jako dane wyjściowe.

Aby uzyskać więcej informacji, zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Tworzenie i monitorowanie potoków
Można tworzyć potoki przy użyciu jednej z następujących narzędzi lub zestawów SDK.

- Kreator kopiowania.
- Azure Portal
- Visual Studio
- Azure PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz następujące samouczki krok po kroku dotyczące tworzenia potoków przy użyciu jednej z następujących narzędzi lub zestawów SDK.

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku za pomocą działania przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Gdy potoku jest tworzone lub wdrożone, można zarządzać i monitorowania potoków przy użyciu bloków w witrynie Azure portal lub aplikacji monitorowanie i zarządzanie nimi. Zobacz następujące tematy, aby uzyskać instrukcje krok po kroku.

- [Monitorowanie potoków i zarządzanie nimi przy użyciu bloków w witrynie Azure portal](data-factory-monitor-manage-pipelines.md).
- [Monitorowanie potoków i zarządzanie nimi przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Jednorazowa potoku
Można tworzyć i zaplanować okresowe uruchamianie potoku (na przykład: co godzinę lub codziennie) w ciągu godziny rozpoczęcia i zakończenia określonego w definicji potoku. Zobacz Planowanie działań, aby uzyskać szczegółowe informacje. Można również utworzyć potok, który jest uruchamiany tylko raz. Aby to zrobić, należy ustawić **pipelineMode** właściwości w definicji potoku, aby **jednorazowej** jak pokazano w następującym przykładzie JSON. Wartość domyślna tej właściwości to **zaplanowane**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Pamiętaj o następujących kwestiach:

* **Rozpocznij** i **zakończenia** nieokreślonych godziny dla potoku.
* **Dostępność** z danych wejściowych i wyjściowych zestawów danych jest określony (**częstotliwość** i **interwał**), nawet jeśli fabryka danych używa wartości.
* Widok diagramu jednorazowe potoki nie są wyświetlane. To zachowanie jest celowe.
* Nie można zaktualizować jednorazowe potoków. Można sklonować potoku jednorazowego, zmień jej nazwę, zaktualizuj właściwości i wdrożyć ją, aby utworzyć nową.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu.
- Aby uzyskać więcej informacji na temat sposobu planowania i wykonywania potoków, zobacz [planowanie i wykonywanie w usłudze Azure Data Factory](data-factory-scheduling-and-execution.md) artykułu.
