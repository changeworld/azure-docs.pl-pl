---
title: Utwórz zadania przygotowanie zadania i ukończone zadania w węzłach obliczeniowych — usługi Azure Batch | Dokumentacja firmy Microsoft
description: Użyj poziom zadania przygotowania zadania, aby zminimalizować transfer danych do węzłów obliczeniowych w usłudze Azure Batch, a następnie zwolnij zadań dla węzła oczyszczania po ukończeniu zadania.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776210"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Uruchamianie zadań przygotowania i zwolnienia zadań w usłudze Batch węzłów obliczeniowych

 Zadanie usługi Azure Batch często wymaga pewnego rodzaju instalacji przed jego zadania podrzędne są wykonywane, a po utworzeniu zadania konserwacji, po zakończeniu jego zadań podrzędnych. Może być konieczne, Pobierz typowych zadań, danych wejściowych do węzłów obliczeniowych lub przekazać dane wyjściowe zadań do usługi Azure Storage, po ukończeniu zadania. Możesz użyć **zadania przygotowania** i **zadania wersji** zadania do wykonania tych operacji.

## <a name="what-are-job-preparation-and-release-tasks"></a>Co to są przygotowanie zadania i zadania zwolnienia?
Przed uruchomieniem zadania podrzędne, zadanie przygotowania zadania jest uruchamiane na wszystkich węzłach obliczeniowych zaplanowanych do uruchamiania co najmniej jedno zadanie. Po zakończeniu zadania zadanie zwolnienia zadania jest uruchamiane w każdym węźle w puli która wykonała przynajmniej jedno zadanie. Podobnie jak w przypadku normalnych zadania podrzędne usługi Batch można określić w wierszu polecenia można wywołać po uruchomieniu zadania przygotowania i zwolnienia zadania.

Zadania przygotowania i zwolnienia zadań oferują dobrze znanych funkcji zadania usługi Batch, takie jak pobieranie plików ([pliki zasobów][net_job_prep_resourcefiles]), z podwyższonym poziomem uprawnień wykonywanie, niestandardowe zmienne środowiskowe, maksymalny czas trwania wykonywania, spróbuj ponownie Liczba i czas przechowywania pliku.

W poniższych sekcjach dowiesz się, jak używać [JobPreparationTask] [ net_job_prep] i [JobReleaseTask] [ net_job_release] klasy znalezione w [ Batch .NET] [ api_net] biblioteki.

> [!TIP]
> Zadania przygotowania i zwolnienia zadań są szczególnie przydatne w środowiskach "udostępnione puli", w których pula węzłów obliczeniowych utrzymuje się między uruchomionych zadań i jest używana przez wiele zadań.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kiedy należy używać przygotowanie zadania i zwolnić zadania
Zadania przygotowania i zwolnienia zadań są dobrym rozwiązaniem w następujących sytuacjach:

**Pobierz dane typowe zadania**

Zadania usługi Batch często wymagają ze wspólnego zestawu danych jako dane wejściowe dla zadania. Na przykład w codzienne obliczenia ryzyka analizy danych rynkowych jest specyficzne dla zadania, jeszcze wspólne dla wszystkich zadań w ramach zadania. Te dane na rynku, rozmiar, często kilku gigabajtów powinny być pobierane do każdego węzła obliczeniowego tylko raz, aby można go używać przez każde zadanie podrzędne uruchamiane w węźle. Użyj **zadania podrzędnego przygotowania zadania** do pobrania tych danych do każdego węzła, zanim wykonywanie zadania przez inne zadania.

**Usuwanie danych wyjściowych zadań i zadań**

W środowisku "udostępnione puli", w którym węzłów obliczeniowych w puli nie zostaną zlikwidowane między zadaniami, może być konieczne usunięcie danych zadania między działa. Może być konieczne zaoszczędzić miejsce na dysku w węzłach lub spełnia zasady zabezpieczeń Twojej organizacji. Użyj **zadanie podrzędne zwolnienia zadania** do usuwania danych, który został pobrany przez zadanie podrzędne przygotowania zadania lub generowane podczas wykonywania zadania.

**Przechowywanie dzienników**

Możesz chcieć przechowywać kopię plików dziennika, które są generowane przez podzadania lub może ulec awarii pliki zrzutu, które mogą być generowane przez aplikacje zakończone niepowodzeniem. Użyj **zadanie podrzędne zwolnienia zadania** w takich przypadkach kompresję i przekazywanie tych danych do [usługi Azure Storage] [ azure_storage] konta.

> [!TIP]
> Innym sposobem, aby utrwalić dzienników i innych zadań i zadań, danych wyjściowych danych jest użycie [Konwencji plików usługi Batch Azure](batch-task-output.md) biblioteki.
> 
> 

## <a name="job-preparation-task"></a>Zadanie przygotowania zadania
Przed wykonaniem zadania usługa Batch wykonuje zadania podrzędnego przygotowania zadania w każdym węźle obliczeniowym, który jest zaplanowane do uruchomienia zadania. Domyślnie usługa Batch oczekuje na zadanie przygotowania zadania, należy wykonać przed uruchomieniem zadania zaplanowane do wykonania w węźle. Można jednak skonfigurować usługi aby nie czekać. Jeśli węzeł zostanie ponownie uruchomiony, zadanie przygotowania zadania jest uruchamiane ponownie, ale można również wyłączyć to zachowanie.

Zadanie przygotowania zadania jest wykonywane tylko w przypadku węzłów, które są planowane do uruchomienia zadania. Zapobiega to niepotrzebne wykonanie zadanie podrzędne przygotowania w przypadku, gdy węzeł nie jest przypisany zadania. Taka sytuacja może wystąpić, gdy liczba zadań w zadaniu jest mniejsza niż liczba węzłów w puli. Ma również zastosowanie, gdy [wykonywanie zadań jednoczesnych](batch-parallel-node-tasks.md) jest włączone, dlatego jeśli bezczynności niektóre węzły liczba zadań jest niższa niż łączna liczba możliwych współbieżnych zadań. Uruchamiając nie zadania podrzędnego przygotowania zadania w węzłach bezczynności, spędzisz mniej pieniędzy na opłaty za transfer danych.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] różni się od [CloudPool.StartTask] [ pool_starttask] się JobPreparationTask wykonuje na początku każdego zadania, natomiast funkcja StartTask wykonuje tylko kiedy węzeł obliczeniowy najpierw łączy puli lub ponowne uruchomienie.
> 
> 

## <a name="job-release-task"></a>Zadanie podrzędne zwolnienia zadania
Gdy zadanie jest oznaczane jako ukończone, zadanie podrzędne zwolnienia zadania jest wykonywana w każdym węźle w puli która wykonała przynajmniej jedno zadanie. Możesz oznaczyć zadanie jako wykonane przez wystawienie otrzymała żądanie przerwania. Następnie usługa Batch ustawi stan zadania *przerywa*, kończy się żadnych aktywnych zadań podrzędnych uruchomionych lub skojarzone z zadaniem i jest uruchamiane zadanie podrzędne zwolnienia zadania. Zadanie jest następnie przechodzi do *ukończone* stanu.

> [!NOTE]
> Usuwanie zadania wykonuje też zadanie podrzędne zwolnienia zadania. Jednak jeśli zadanie zostało już zakończone, zwolnienie zadania nie uruchomieniu po raz drugi Jeśli zadanie później zostanie usunięty.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Zadania przygotowania bazy i wersji zadania za pomocą platformy .NET usługi Batch
Aby użyć zadania przygotowania zadania, przypisywać [JobPreparationTask] [ net_job_prep] obiektu zadania [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] właściwości. Podobnie, zainicjować [JobReleaseTask] [ net_job_release] i przypisz je do zadania [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] właściwości do ustawienia zadania Zwolnienie zadania.

W poniższym przykładzie `myBatchClient` jest wystąpieniem [BatchClient][net_batch_client], i `myPool` jest istniejącej puli w ramach konta usługi Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Jak wspomniano wcześniej, podrzędne zwolnienia zadania jest wykonywany, gdy zadanie zostało przerwane lub usunięte. Zakończenie zadania za pomocą [JobOperations.TerminateJobAsync][net_job_terminate]. Usuń zadanie [JobOperations.DeleteJobAsync][net_job_delete]. Zwykle zakończyć lub usunąć zadanie po zakończeniu jego zadań podrzędnych, lub gdy został osiągnięty limit czasu, który został zdefiniowany.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Przykładowy kod w serwisie GitHub
Aby zapoznać się przygotowanie zadania i zwolnienie zadania w działaniu, zapoznaj się z [JobPrepRelease] [ job_prep_release_sample] przykładowy projekt w witrynie GitHub. Ta aplikacja konsolowa wykonuje następujące czynności:

1. Tworzy pulę, z dwoma węzłami.
2. Tworzy zadanie przygotowanie zadania, wersji i zadań standardowych.
3. Uruchamia zadanie przygotowania zadania, które najpierw zapisuje identyfikator węzła do pliku tekstowego w katalogu "udostępnione" węzła.
4. Uruchamia zadanie w każdym węźle, który zapisuje jego identyfikator zadania: do tego samego pliku tekstowego.
5. Po wykonaniu wszystkich zadań (lub zostanie osiągnięty limit czasu), drukuje zawartość pliku tekstowego każdego węzła do konsoli.
6. Po ukończeniu zadania jest uruchamiane zadanie podrzędne zwolnienia zadania do usunięcia pliku z węzła.
7. Wyświetla kody zakończenia zadania przygotowania i zwolnienia zadań dla każdego węzła, na którym są wykonywane.
8. Wstrzymuje wykonywanie umożliwia potwierdzenie usunięcia zadania i/lub puli.

Dane wyjściowe z przykładowej aplikacji będą podobne do następujących:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Ze względu na zmiennej tworzenia i uruchamiania czasu węzłów w nowej puli (niektóre węzły są gotowe do zadań przed innymi) mogą pojawić się różne wyniki. Ściślej mówiąc ponieważ szybkiego zakończenia zadania, jednym z węzłów w puli może wykonywać wszystkie zadania podrzędne. W takim przypadku można zauważyć, że zadania przygotowania i zwolnienia zadania nie istnieją dla węzła, który jest wykonywane żadne zadania.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Zbadaj zadania przygotowania i zwolnienia zadania w witrynie Azure portal
Po uruchomieniu aplikacji przykładowej, możesz użyć [witryny Azure portal] [ portal] można wyświetlić właściwości zadania i jego zadań podrzędnych, a nawet pobierać plik tekstowy udostępnionego, który jest modyfikowany przez zadania podrzędne.

Zrzut ekranu poniżej przedstawiono **bloku zadań przygotowywania** w witrynie Azure portal po uruchomieniu aplikacji przykładowej. Przejdź do *JobPrepReleaseSampleJob* właściwości po zakończeniu zadania (ale przed usunięciem z zadania i puli) i kliknij przycisk **przygotowania** lub **zadaniazwolnienia**Aby wyświetlić jego właściwości.

![Właściwości przygotowanie zadania w witrynie Azure portal][1]

## <a name="next-steps"></a>Kolejne kroki
### <a name="application-packages"></a>Pakiety aplikacji
Oprócz zadania podrzędnego przygotowania zadania umożliwia także [pakiety aplikacji](batch-application-packages.md) funkcji usługi Batch w celu przygotowania węzłów obliczeniowych do wykonywania zadań. Ta funkcja jest szczególnie przydatne w przypadku wdrażania aplikacji, które nie wymagają uruchamiania Instalatora, aplikacje, które zawierają wiele plików (ponad 100) lub aplikacji, które wymagają kontroli wersji z ograniczeniami.

### <a name="installing-applications-and-staging-data"></a>Instaluje aplikacje i przemieszcza dane
Ten wpis na forum MSDN zawiera omówienie kilku metod przygotowania węzłów do uruchamiania zadań:

[Instaluje aplikacje i przemieszcza dane w usłudze Batch węzłów obliczeniowych][forum_post]

Zapisane przez jeden z członków zespołu usługi Azure Batch, omówiono w nim kilka technik, które służy do wdrażania aplikacji i danych w węzłach obliczeniowych.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
