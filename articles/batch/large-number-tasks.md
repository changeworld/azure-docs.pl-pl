---
title: Przesyłanie dużej liczby zadań — usługa Azure Batch | Dokumenty firmy Microsoft
description: Jak skutecznie przesłać bardzo dużą liczbę zadań w jednym zadaniu usługi Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026051"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Przesyłanie dużej liczby zadań do zadania wsadowego

Po uruchomieniu dużych obciążeń usługi Azure Batch, można przesłać dziesiątki tysięcy, setki tysięcy lub nawet więcej zadań do jednego zadania. 

W tym artykule przedstawiono wskazówki i kilka przykładów kodu do przesyłania dużej liczby zadań ze znacznie zwiększoną przepływnością do jednego zadania batch. Po przesłaniu zadań wchodzą one w kolejce partii do przetwarzania w puli określonej dla zadania.

## <a name="use-task-collections"></a>Używanie kolekcji zadań

Wsadowe interfejsy API zapewniają metody efektywnego dodawania zadań do zadania jako *kolekcji,* oprócz jednego naraz. Podczas dodawania dużej liczby zadań, należy użyć odpowiednich metod lub przeciążenia, aby dodać zadania jako kolekcję. Ogólnie rzecz biorąc można skonstruować kolekcji zadań przez definiowanie zadań podczas iterowania przez zestaw plików wejściowych lub parametrów dla zadania.

Maksymalny rozmiar kolekcji zadań, który można dodać w jednym wywołaniu, zależy od używanego interfejsu API usługi Batch:

* Następujące interfejsy API partii ograniczają kolekcję do **100 zadań**. Limit może być mniejszy w zależności od rozmiaru zadań — na przykład, jeśli zadania mają dużą liczbę plików zasobów lub zmiennych środowiskowych.

    * [INTERFEJS API ODPOCZYNKU](/rest/api/batchservice/task/addcollection)
    * [Interfejs API języka Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Podczas korzystania z tych interfejsów API, należy podać logikę, aby podzielić liczbę zadań, aby spełnić limit kolekcji i do obsługi błędów i ponownych prób w przypadku, gdy dodawanie zadań nie powiedzie się. Jeśli kolekcja zadań jest zbyt duża, aby dodać, żądanie generuje błąd i powinny być ponownie ponowione z mniejszą liczbą zadań.

* Następujące interfejsy API obsługują znacznie większe kolekcje zadań — ograniczone tylko dostępnością pamięci RAM na kliencie przesyłającym. Te interfejsy API w sposób przezroczysty obsługi dzielenia kolekcji zadań na "fragmenty" dla interfejsów API niższego poziomu i ponownych prób, jeśli dodanie zadań nie powiedzie się.

    * [Interfejs API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Interfejs API języka Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch](batch-cli-templates.md) z szablonami interfejsu wiersza polecenia usługi Batch
    * [Rozszerzenie SDK języka Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zwiększenie przepływności przesyłania zadań

Może upłynąć trochę czasu, aby dodać dużą kolekcję zadań do zadania — na przykład do 1 minuty, aby dodać 20 000 zadań za pośrednictwem interfejsu API platformy .NET. W zależności od interfejsu API partii i obciążenia można poprawić przepływność zadań, modyfikując co najmniej jedną z następujących czynności:

* **Rozmiar zadania** — dodawanie dużych zadań trwa dłużej niż dodawanie mniejszych. Aby zmniejszyć rozmiar każdego zadania w kolekcji, można uprościć wiersz polecenia zadania, zmniejszyć liczbę zmiennych środowiskowych lub obsługiwać wymagania dotyczące wykonywania zadań bardziej efektywnie. Na przykład zamiast dużej liczby plików zasobów, zainstaluj zależności zadań przy użyciu [zadania startowego](batch-api-basics.md#start-task) w puli lub użyj [pakietu aplikacji](batch-application-packages.md) lub [kontenera platformy Docker](batch-docker-container-workloads.md).

* **Liczba operacji równoległych** — w zależności od interfejsu API partii, zwiększyć przepływność, zwiększając maksymalną liczbę równoczesnych operacji przez klienta usługi Batch. Skonfiguruj to ustawienie przy użyciu właściwości [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) w interfejsie API platformy .NET lub `threads` parametru metod, takich jak [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) w rozszerzeniu SDK języka Pniu Pythona. (Ta właściwość nie jest dostępna w natywnym SDK języka Python.) Domyślnie ta właściwość jest ustawiona na 1, ale ustawić go wyżej, aby poprawić przepływność operacji. Można handlować zwiększoną przepustowość przez zużywanie przepustowości sieci i niektóre wydajności procesora. Przepustowość zadania zwiększa się nawet o `MaxDegreeOfParallelism` 100 razy więcej niż lub . `threads` W praktyce należy ustawić liczbę równoczesnych operacji poniżej 100. 
 
  Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch z szablonami usługi Batch zwiększa liczbę równoczesnych operacji automatycznie na podstawie liczby dostępnych rdzeni, ale ta właściwość nie jest konfigurowana w wierszu polecenia. 

* **Limity połączeń HTTP** — liczba równoczesnych połączeń HTTP może ograniczać wydajność klienta usługi Batch podczas dodawania dużej liczby zadań. Liczba połączeń HTTP jest ograniczona w przypadku niektórych interfejsów API. Podczas tworzenia za pomocą interfejsu API platformy .NET, na przykład [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) właściwość jest ustawiona na 2 domyślnie. Zaleca się zwiększenie wartości do liczby zbliżonej lub większej niż liczba operacji równoległych.

## <a name="example-batch-net"></a>Przykład: Partia .NET

Następujące fragmenty kodu języka C# pokazują ustawienia do skonfigurowania podczas dodawania dużej liczby zadań przy użyciu interfejsu API usługi Batch .NET.

Aby zwiększyć przepływność zadań, zwiększ wartość [właściwości MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) właściwości [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Przykład:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Dodaj kolekcję zadań do zadania przy użyciu odpowiedniego przeciążenia [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) lub [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metody. Przykład:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Przykład: rozszerzenie wiersza polecenia partii

Korzystając z rozszerzeń interfejsu wiersza polecenia usługi Azure Batch z [szablonami interfejsu wiersza polecenia partii,](batch-cli-templates.md)utwórz plik JSON szablonu zadania zawierający [fabrykę zadań](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Fabryka zadań konfiguruje kolekcję powiązanych zadań dla zadania z definicji pojedynczego zadania.  

Poniżej przedstawiono przykładowy szablon zadania dla jednowymiarowego parametrycznego zadania wyciągnięcia po ścieżce z dużą liczbą zadań — w tym przypadku 250 000. Wiersz polecenia zadania jest `echo` prostym poleceniem.

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
Aby uruchomić zadanie z szablonem, zobacz [Używanie szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Przykład: rozszerzenie SDK języka Python wsadowym

Aby użyć rozszerzenia azure batch Python SDK, najpierw zainstaluj zestaw SDK języka Python i rozszerzenie:

```
pip install azure-batch
pip install azure-batch-extensions
```

Skonfiguruj `BatchExtensionsClient` rozszerzenie SDK, które używa:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Tworzenie kolekcji zadań do dodania do zadania. Przykład:


```python
tasks = list()
# Populate the list with your tasks
...
```

Dodaj kolekcję zadań za pomocą [pliku task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ustaw `threads` parametr, aby zwiększyć liczbę równoczesnych operacji:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozszerzenie zestawu SDK języka Python w języku wsadowym obsługuje również dodawanie parametrów zadań do zadania przy użyciu specyfikacji JSON dla fabryki zadań. Na przykład należy skonfigurować parametry zadania dla parametrycznego wyciągnięcia po ścieżce podobnej do tej w poprzednim przykładzie szablonu interfejsu wiersza polecenia partii:

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
                        "retentionTime": "PT1H"
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

Dodaj parametry zadania do zadania. Ustaw `threads` parametr, aby zwiększyć liczbę równoczesnych operacji:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o używaniu rozszerzenia interfejsu wiersza polecenia usługi Azure Batch z [szablonami interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md).
* Dowiedz się więcej o [rozszerzeniu SDK języka Python batch](https://pypi.org/project/azure-batch-extensions/).
