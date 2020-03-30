---
title: Tworzenie/planowanie potoków, działania łańcuchowe w fabryce danych
description: Dowiedz się, jak utworzyć potok danych w usłudze Azure Data Factory, aby przenosić i przekształcać dane. Utwórz przepływ pracy oparty na danych, aby tworzyć gotowe do użycia informacje.
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
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281524"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Potoki i działania w fabryce danych platformy Azure
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-create-pipelines.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Potoki w wersji 2](../concepts-pipelines-activities.md).

Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz z konstruowaniem za ich pomocą pełnych przepływów pracy dla scenariuszy przenoszenia i przetwarzania danych.

> [!NOTE]
> W tym artykule przyjęto założenie, że przeszedłeś [wprowadzenie do usługi Azure Data Factory.](data-factory-introduction.md) Jeśli nie masz praktyczne doświadczenie z tworzeniem fabryk danych, przechodząc przez [samouczek transformacji danych](data-factory-build-your-first-pipeline.md) i/lub [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pomoże Ci lepiej zrozumieć ten artykuł.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Możesz na przykład użyć działania kopiowania w celu skopiowania danych z lokalnego programu SQL Server do usługi Azure Blob Storage. Następnie użyj działania usługi Hive, które uruchamia skrypt Hive w klastrze usługi Apache HDInsight w celu przetworzenia/przekształcenia danych z magazynu obiektów blob, aby utworzyć dane wyjściowe. Na koniec użyj drugiego działania kopiowania w celu skopiowania danych wyjściowych do usługi Microsoft Azure SQL Data Warehouse, na podstawie której tworzone są rozwiązania raportowania analizy biznesowej (BI).

Dane działanie może — ale nie musi — korzystać z wejściowych [zestawów danych](data-factory-create-datasets.md) i generować co najmniej jeden wyjściowy [zestaw danych](data-factory-create-datasets.md). Na poniższym diagramie przedstawiono relację między potokiem, działaniem i zestawem danych w usłudze Data Factory:

![Relacja między potokiem, aktywnością i zestawem danych](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Potok umożliwia zarządzanie działaniami jako zestaw, a nie każdy z nich indywidualnie. Na przykład można wdrożyć, zaplanować, zawiesić i wznowić potok, zamiast zajmować się działaniami w potoku niezależnie.

Usługa Data Factory obsługuje dwa typy działań — w zakresie przekształcania oraz przenoszenia danych. Każde działanie może mieć zero lub więcej wejściowych [zestawów danych](data-factory-create-datasets.md) i utworzyć jeden lub więcej wyjściowych zestawów danych.

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
Jeśli chcesz przenieść dane do/z magazynu danych, którego działanie kopiowania nie obsługuje, lub przekształcić dane przy użyciu własnej logiki, utwórz **niestandardowe działanie platformy .NET**. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

## <a name="schedule-pipelines"></a>Planowanie potoków
Potok jest aktywny tylko między jego czas **rozpoczęcia** i **zakończenia.** Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymany, nie zostanie wykonany niezależnie od jego czasu rozpoczęcia i zakończenia. Aby potok uruchomić, nie należy wstrzymać. Zobacz [Planowanie i wykonywanie,](data-factory-scheduling-and-execution.md) aby dowiedzieć się, jak działa planowanie i wykonanie w fabryce danych platformy Azure.

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

| Tag | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa potoku. Określ nazwę, która reprezentuje akcję wykonywaną przez potok. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się od cyfry\_litery lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">",",\*","%","&,"":",","\\</li></ul> |Tak |
| description | Wprowadź tekst opisujący przeznaczenie potoku. |Tak |
| activities | W sekcji **activities** można zdefiniować jedno lub więcej działań. Zobacz następną sekcję, aby uzyskać szczegółowe informacje na temat działań JSON element. | Tak |
| rozpoczynanie | Data rozpoczęcia dla potoku. Musi być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: `2016-10-14T16:32:41Z`. <br/><br/>Możliwe jest określenie czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00-05:00`", który jest 6 AM EST.<br/><br/>Właściwości początkowe i końcowe razem określają aktywny okres dla potoku. Wycinki wyjściowe są produkowane tylko w tym aktywnym okresie. |Nie<br/><br/>Jeśli określisz wartość właściwości końcowej, należy określić wartość właściwości start.<br/><br/>Godziny rozpoczęcia i zakończenia mogą być puste, aby utworzyć potok. Należy określić obie wartości, aby ustawić aktywny okres do uruchomienia potoku. Jeśli nie określisz godziny rozpoczęcia i zakończenia podczas tworzenia potoku, można ustawić je przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod później. |
| end | Data-godzina zakończenia dla potoku. Jeśli jest określony musi być w formacie ISO. Na przykład: `2016-10-14T17:32:41Z` <br/><br/>Możliwe jest określenie czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00-05:00`, który jest 6 AM EST.<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. <br/><br/> Potok jest aktywny tylko między jego czas rozpoczęcia i zakończenia. Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymany, nie zostanie wykonany niezależnie od jego czasu rozpoczęcia i zakończenia. Aby potok uruchomić, nie należy wstrzymać. Zobacz [Planowanie i wykonywanie,](data-factory-scheduling-and-execution.md) aby dowiedzieć się, jak działa planowanie i wykonanie w fabryce danych platformy Azure. |Nie <br/><br/>Jeśli określisz wartość właściwości start, należy określić wartość właściwości końcowej.<br/><br/>Zobacz notatki dla właściwości **start.** |
| isPaused (Nieusło) | Jeśli ustawiona wartość true, potok nie zostanie uruchomiony. Jest w stanie wstrzymania. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania potoku. |Nie |
| tryb potoku | Metoda planowania jest uruchamiana dla potoku. Dozwolone wartości to: zaplanowane (domyślnie), jednorazowe.<br/><br/>"Zaplanowane" wskazuje, że potok działa w określonym przedziale czasu zgodnie z jego aktywnym okresem (godzina rozpoczęcia i zakończenia). "Onetime" wskazuje, że potok działa tylko raz. Potoki jednorazowe po utworzeniu nie mogą być modyfikowane/aktualizowane obecnie. Zobacz [potok jednorazowy, aby](#onetime-pipeline) uzyskać szczegółowe informacje o ustawieniu jednorazowym. |Nie |
| expirationTime | Czas po utworzeniu, dla którego [potok jednorazowy](#onetime-pipeline) jest prawidłowy i powinien pozostać aprowizgodzony. Jeśli nie ma żadnych aktywnych, nie powiodło się lub oczekujących uruchomień, potok jest automatycznie usuwany po osiągnięciu czasu wygaśnięcia. Wartość domyślna:`"expirationTime": "3.00:00:00"`|Nie |
| zestawy danych |Lista zestawów danych, które mają być używane przez działania zdefiniowane w potoku. Ta właściwość może służyć do definiowania zestawów danych, które są specyficzne dla tego potoku i nie zdefiniowane w fabryce danych. Zestawy danych zdefiniowane w tym potoku mogą być używane tylko przez ten potok i nie mogą być współużytkowane. Zobacz [zestawy danych o określonym zakresie,](data-factory-create-datasets.md#scoped-datasets) aby uzyskać szczegółowe informacje. |Nie |

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

| Tag | Opis | Wymagany |
| --- | --- | --- |
| name | Nazwa działania. Określ nazwę, która reprezentuje akcję wykonywaną przez działanie. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się od cyfry\_litery lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">",","","%","&,"":","\\</li></ul> |Tak |
| description | Tekst opisujący przeznaczenie działania |Tak |
| type | Typ działania. Zobacz [działania przenoszenia danych](#data-movement-activities) i [działania przekształcania danych](#data-transformation-activities) dla różnych typów działań. |Tak |
| Wejścia |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Tak |
| Wyjść |Tabele danych wyjściowych używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Tak |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak w przypadku działania usługi HDInsight i działania oceniania wsadowego usługi Azure Machine Learning <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji **typeProperties** zależą od typu działania. Aby wyświetlić właściwości typu dla działania, kliknij linki do działań w poprzedniej sekcji. | Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określony, używane są domyślne zasady. |Nie |
| scheduler | "scheduler" właściwość służy do definiowania żądanego planowania dla działania. Jego właściwości podrzędne są takie same jak te we [właściwości dostępności we właściwości](data-factory-create-datasets.md#dataset-availability)zestawu danych . |Nie |

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
* W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. W sekcji [Działania związane z przenoszeniem danych](#data-movement-activities) kliknij magazyn danych, którego chcesz użyć jako źródła lub ujścia, aby dowiedzieć się więcej o przenoszeniu danych do/z tego magazynu danych.

Aby uzyskać pełny instruktaż tworzenia tego potoku, zobacz [Samouczek: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Sekcja `defines` służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu `${hiveconf:partitionedtable}`gałęzi jako wartości konfiguracji gałęzi (np. `${hiveconf:inputtable}`

Sekcja **typeProperties** jest inna dla każdego działania przekształcania. Aby dowiedzieć się więcej o właściwościach typu obsługiwanych dla działania transformacji, kliknij działanie transformacji w tabeli [Działania przekształcania danych.](#data-transformation-activities)

Aby uzyskać pełną instruktaż tworzenia tego potoku, zobacz [Samouczek: Tworzenie pierwszego potoku do przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Poprzednie dwa przykładowe potoki zawierają tylko po jednym działaniu. Potok może obejmować więcej niż jedno działanie.

Jeśli masz wiele działań w potoku i dane wyjściowe działania nie jest dane wejściowe innego działania, działania mogą działać równolegle, jeśli wycinki danych wejściowych dla działań są gotowe.

Można łańcuch dwa działania, o wyjściowym zestawie danych jednego działania jako wejściowego zestawu danych innego działania. Drugie działanie jest wykonywane tylko wtedy, gdy pierwsze zakończy się pomyślnie.

![Tworzenie łańcucha działań w tym samym potoku](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

W tym przykładzie potoku ma dwa działania: Activity1 i Activity2. Activity1 przyjmuje Dataset1 jako dane wejściowe i tworzy wyjściowy zestaw danych2. Działanie przyjmuje Dataset2 jako dane wejściowe i tworzy wyjściowy zestaw danych3. Ponieważ dane wyjściowe Activity1 (Dataset2) jest dane wejściowe Activity2, Activity2 jest uruchamiany tylko po zakończeniu działania i tworzy plasterek Dataset2. Jeśli Działanie1 nie powiedzie się z jakiegoś powodu i nie tworzy plasterek Dataset2, działanie 2 nie jest uruchamiane dla tego plasterka (na przykład: 9:00 do 10:00).

Można również łańcuch działań, które znajdują się w różnych potoków.

![Łączenie działań w dwóch rurociągach](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

W tym przykładzie Pipeline1 ma tylko jedno działanie, które przyjmuje Dataset1 jako dane wejściowe i tworzy Dataset2 jako dane wyjściowe. Pipeline2 ma również tylko jedno działanie, które przyjmuje Dataset2 jako dane wejściowe i Dataset3 jako dane wyjściowe.

Aby uzyskać więcej informacji, zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Tworzenie i monitorowanie potoków
Potoki można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK.

- Kreator kopiowania
- Visual Studio
- Azure PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz poniższe samouczki, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków przy użyciu jednego z tych narzędzi lub zestawów SDK.

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku z działaniem przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu/wdrożeniu potoku można zarządzać potokami i monitorować je przy użyciu bloków portalu Azure lub monitoruj i zarządzaj aplikacją. Zapoznaj się z poniższymi tematami, aby uzyskać instrukcje krok po kroku.

- [Monitoruj potoki i zarządzaj nimi przy użyciu bloków portalu Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorowanie potoków i zarządzanie nimi za pomocą funkcji Monitoruj i zarządzaj aplikacją](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Rurociąg jednorazowy
Można utworzyć i zaplanować okresowe uruchamianie potoku (na przykład: co godzinę lub codziennie) w godzinach rozpoczęcia i zakończenia określonych w definicji potoku. Zobacz Planowanie działań, aby uzyskać szczegółowe informacje. Można również utworzyć potok, który działa tylko raz. W tym celu należy ustawić **pipelineMode** właściwość w definicji potoku do **jednorazowego,** jak pokazano w poniższym przykładzie JSON. Wartość domyślna dla tej właściwości jest **zaplanowana**.

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

* **Godziny rozpoczęcia** i **zakończenia** potoku nie są określone.
* **Określono dostępność** wejściowych i wyjściowych zestawów danych **(częstotliwość** i **interwał),** nawet jeśli usługa Data Factory nie używa wartości.
* Widok diagramu nie pokazuje potoków jednorazowych. To zachowanie jest celowe.
* Nie można zaktualizować potoków jednorazowych. Można sklonować potok jednorazowy, zmienić jego nazwę, zaktualizować właściwości i wdrożyć go, aby utworzyć inny.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat zestawów danych, zobacz [Artykuł Tworzenie zestawów danych.](data-factory-create-datasets.md)
- Aby uzyskać więcej informacji na temat planowania i wykonywania potoków, zobacz Planowanie i wykonywanie w artykule [Usługi Azure Data Factory.](data-factory-scheduling-and-execution.md)
