---
title: Przesyłanie dużej liczby zadań — Azure Batch | Dokumentacja firmy Microsoft
description: Sposób wydajnego przesyłania dużej liczby zadań w ramach jednego zadania usługi Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: ed04774969f72f1d6037a350f019d81d812d73f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549669"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Przesyłanie dużej liczby zadań do zadania usługi Batch

Po uruchomieniu na dużą skalę obciążeń usługi Azure Batch, można przesłać dziesiątków tysięcy setki tysięcy lub nawet więcej zadań do pojedynczego zadania. 

Ten artykuł zawiera wskazówki i przykłady kodu do przesyłania dużej liczby zadań przy użyciu znacznie większą przepływność do jednego zadania usługi Batch. Po przesłaniu zadania wejdzie kolejki usługi Batch do przetwarzania w puli, które określisz dla zadania.

## <a name="use-task-collections"></a>Używanie kolekcji zadań

Interfejsy API usługi Batch zapewniają metody i efektywne Dodawanie podzadań do zadania jako *kolekcji*, oprócz pojedynczo. Podczas dodawania dużej liczby zadań, należy używać właściwe metody lub przeciążenia można dodać zadania jako kolekcja. Ogólnie rzecz biorąc konstruuje kolekcję zadań, definiując zadania, jak iteracyjne zestaw pliki wejściowe lub parametry dla zadania.

Maksymalny rozmiar kolekcji zadań, który można dodać w pojedynczym wywołaniu zależy od interfejsu API usługi Batch, możesz użyć:

* Następujące interfejsy API usługi Batch limit kolekcji **100 zadań podrzędnych**. Limit może być mniejszy w zależności od rodzaju zadania — na przykład, jeśli zadania mają dużą liczbę plików zasobów lub zmienne środowiskowe.

    * [Interfejs API REST](/rest/api/batchservice/task/addcollection)
    * [Interfejs API języka Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Interfejsu API środowiska node.js](/javascript/api/azure-batch/task?view=azure-node-latest)

  Korzystając z tych interfejsów API, musisz zapewnić logikę można podzielić liczbę zadań, aby spełnić limit zbioru i do obsługi błędów i ponawianie prób w przypadku dodawania zadań zakończy się niepowodzeniem. Jeśli kolekcja zadań jest zbyt duży, aby dodać, żądanie generuje błąd i powinno być ponowione, mniejszej liczby zadań.

* Następujące interfejsy API obsługuje znacznie większe kolekcji zadań — ograniczony tylko ilością dostępność pamięci RAM na przesyłanie klienta. Te interfejsy API obsługują przezroczyste podzielenie kolekcji zadań na "fragmenty" dla niskiego poziomu interfejsy API i ponownych prób, jeśli dodanie zadania nie powiodło się.

    * [Interfejs API programu .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Interfejs API języka Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Rozszerzenie usługi Azure Batch CLI](batch-cli-templates.md) z szablonów interfejsu wiersza polecenia usługi Batch
    * [Rozszerzenia zestawu SDK języka Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zwiększyć przepustowość przesyłania zadań

To może zająć trochę czasu, aby dodać to duży zbiór zadań podrzędnych do zadania — na przykład, się na 1 minutę, aby dodać 20 000 zadań, za pośrednictwem interfejsu API platformy .NET. W zależności od interfejsu API usługi Batch i obciążenia można zwiększyć przepływność zadania, zmieniając co najmniej jeden z następujących czynności:

* **Zadanie rozmiar** — Dodawanie rozbudowanych zadań trwa dłużej niż dodawanie mniejszymi. Aby zmniejszyć rozmiar poszczególnych zadań w kolekcji, można uproszczenie zadań wiersza polecenia, Zmniejsz liczbę zmiennych środowiskowych lub bardziej efektywnie obsługiwać wymagania dotyczące wykonywania zadania. Na przykład, zamiast korzystać z dużą liczbą plików zasobów, należy zainstalować zależności zadań podrzędnych przy użyciu [zadanie podrzędne uruchamiania](batch-api-basics.md#start-task) na pulę lub użycia [pakiet aplikacji](batch-application-packages.md) lub [kontener platformy Docker](batch-docker-container-workloads.md).

* **Liczba operacji równoległych** — w zależności od interfejsu API usługi Batch, zwiększenie przepływności przez zwiększenie maksymalnej liczby równoczesnych operacji przez klienta usługi Batch. Skonfiguruj to ustawienie przy użyciu [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) właściwość w interfejsie API .NET lub `threads` parametru metody takie jak [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)w rozszerzeniu zestawu SDK Python usługi Batch. (Ta właściwość nie jest dostępna w natywnym zestawem SDK Python usługi Batch). Domyślnie ta właściwość jest ustawiona na 1, ale większa zwiększyć przepływność operacji. Równoważących się zwiększać przepustowość przez konsumencki przepustowości sieci i wydajności procesora CPU. Przepływność zadań zwiększa się o rozmiarze do 100 razy `MaxDegreeOfParallelism` lub `threads`. W praktyce należy ustawić liczbę równoczesnych operacji niższych niż 100. 
 
  Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch przy użyciu szablonów usługi Batch zwiększa liczby operacji jednoczesnych, automatycznie w oparciu o liczbę dostępnych rdzeni, ale ta właściwość nie jest konfigurowany w interfejsie wiersza polecenia. 

* **Limity połączeń HTTP** -ograniczać liczbę jednoczesnych połączeń HTTP wydajność klienta usługi Batch podczas jego polega na dodaniu dużą liczbę zadań. Liczba połączeń HTTP jest ograniczona za pomocą określonych interfejsów API. Podczas tworzenia za pomocą interfejsu API programu .NET, na przykład [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) właściwość jest domyślnie ustawiona na 2. Zaleca się zwiększenie wartości na liczbę blisko lub większa niż liczba operacji równoległych.

## <a name="example-batch-net"></a>Przykład: Batch .NET

Poniższe fragmenty kodu C# Pokaż ustawienia konfiguracji podczas dodawania dużej liczby zadań przy użyciu interfejsu API .NET usługi Batch.

Aby zwiększyć przepływność zadań, należy zwiększyć wartość [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) właściwość [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Na przykład:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Dodaj kolekcję zadań do zadania za pomocą odpowiednich przeciążenia [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) lub [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metody. Na przykład:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Przykład: Rozszerzenie interfejsu wiersza polecenia usługi Batch

Korzystanie z rozszerzeń interfejsu wiersza polecenia usługi Azure Batch przy użyciu [szablonów interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md), Utwórz plik JSON szablonu zadania, który zawiera [fabryki zadań](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Fabryka zadań umożliwia skonfigurowanie kolekcji powiązanych zadań dla zadania z definicji pojedynczego zadania.  

Poniżej przedstawiono przykładowy szablon zadania dla zadania jednowymiarowa parametrycznych z dużą liczbą zadań — w tym przypadku 250 000. Wiersz polecenia zadania jest prostą `echo` polecenia.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Aby uruchomić zadanie przy użyciu szablonu, zobacz [użycia usługi Azure Batch CLI templates and i transferu plików](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Przykład: Rozszerzenia zestawu SDK języka Python usługi Batch

Aby użyć rozszerzenia Azure SDK Python usługi Batch, należy najpierw zainstalować zestaw SDK języka Python i rozszerzenia:

```
pip install azure-batch
pip install azure-batch-extensions
```

Konfigurowanie `BatchExtensionsClient` używającej rozszerzenie SDK:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Utwórz kolekcję zadań do dodania do zadania. Na przykład:


```python
tasks=list()
# Populate the list with your tasks
...

```

Dodaj przy użyciu kolekcji zadań [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ustaw `threads` parametru, aby zwiększyć liczbę jednoczesnych operacji:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozszerzenia zestawu SDK Python usługi Batch obsługuje również Dodawanie parametrów zadania do zadania za pomocą specyfikacji formatu JSON dla fabryki zadań. Na przykład skonfigurować parametry zadania parametrycznych podobny do przedstawionego w tym przykładzie szablon interfejsu wiersza polecenia usługi Batch:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Dodaj parametry zadania do zadania. Ustaw `threads` parametru, aby zwiększyć liczbę jednoczesnych operacji:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o za pomocą rozszerzenia wiersza polecenia platformy Azure Batch przy użyciu [szablonów interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md).
* Dowiedz się więcej o [rozszerzenia zestawu SDK Python usługi Batch](https://pypi.org/project/azure-batch-extensions/).
