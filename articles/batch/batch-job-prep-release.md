---
title: Tworzenie zadań w celu przygotowania & wykonania zadań w węzłach obliczeniowych — Usługa Azure Batch
description: Użyj zadań przygotowania na poziomie zadania, aby zminimalizować transfer danych do węzłów obliczeniowych usługi Azure Batch i zwolnić zadania do oczyszczania węzłów po zakończeniu zadania.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d9f6f015c210592d5d8053b1b34d5357bb357629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586788"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Uruchamianie zadań przygotowania zadań i zwalniania zadań w węzłach obliczeniowych wsadowych

 Zadanie usługi Azure Batch często wymaga jakiejś formy konfiguracji przed wykonaniem jego zadań i konserwacji po zakończeniu zadania. Może być konieczne pobranie typowych danych wejściowych zadań do węzłów obliczeniowych lub przekazanie danych wyjściowych zadania do usługi Azure Storage po zakończeniu zadania. Do wykonania tych operacji można użyć zadań **przygotowania zadań** i **zwalniania** zadań.

## <a name="what-are-job-preparation-and-release-tasks"></a>Co to są zadania związane z przygotowaniem i zwolnieniem?
Przed uruchomieniem zadań zadania zadanie przygotowania zadania jest uruchamiane we wszystkich węzłach obliczeniowych zaplanowanych do uruchomienia co najmniej jednego zadania. Po zakończeniu zadania zadanie zwalniania zadania jest uruchamiane w każdym węźle w puli, który wykonał co najmniej jedno zadanie. Podobnie jak w przypadku zwykłych zadań usługi Batch, można określić wiersz polecenia, który ma być wywoływany po uruchomieniu zadania przygotowania lub zwolnienia.

Zadania przygotowania i zwalniania zadania oferują znane funkcje zadań wsadowych, takie jak pobieranie plików[(pliki zasobów),][net_job_prep_resourcefiles]podwyższone wykonanie, niestandardowe zmienne środowiskowe, maksymalny czas wykonywania, liczba ponownych prób i czas przechowywania plików.

W poniższych sekcjach dowiesz się, jak używać [jobpreparationTask][net_job_prep] i [JobReleaseTask][net_job_release] klasy znalezione w [bibliotece Batch .NET.][api_net]

> [!TIP]
> Zadania przygotowania i zwalniania zadań są szczególnie przydatne w środowiskach "puli udostępnionej", w których pula węzłów obliczeniowych będzie się powtarzać między przebiegami zadań i jest używana przez wiele zadań.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kiedy używać zadań związanych z przygotowaniem i zwolnieniem zadań
Zadania związane z przygotowaniem i zwolnieniem z pracy są odpowiednie do następujących sytuacji:

**Pobieranie typowych danych zadań**

Zadania wsadowe często wymagają wspólnego zestawu danych jako danych wejściowych dla zadań zadania zadania. Na przykład w codziennych obliczeniach analizy ryzyka dane rynkowe są specyficzne dla zadania, ale wspólne dla wszystkich zadań w zadaniu. Te dane rynkowe, często kilka gigabajtów w rozmiarze, powinny być pobierane do każdego węzła obliczeniowego tylko raz, tak aby każde zadanie uruchamiane w węźle można go używać. Użyj **zadania przygotowania zadania,** aby pobrać te dane do każdego węzła przed wykonaniem innych zadań zadania.

**Usuwanie zadania i zadania wyjściowego**

W środowisku "puli udostępnionej", w którym węzły obliczeniowe puli nie są likwidowane między zadaniami, może być konieczne usunięcie danych zadania między przebiegami. Może być konieczne zachowanie miejsca na dysku w węzłach lub spełnienie zasad zabezpieczeń organizacji. Zadanie **zwalniania zadania** służy do usuwania danych pobranych przez zadanie przygotowujące do zadania lub wygenerowanych podczas wykonywania zadania.

**Przechowywanie dzienników**

Można zachować kopię plików dziennika, które generują zadania lub być może pliki zrzutu awaryjnego, które mogą być generowane przez aplikacje nie powiodło się. Użyj **zadania zwalniania zadania** w takich przypadkach, aby skompresować i przekazać te dane do konta [usługi Azure Storage.][azure_storage]

> [!TIP]
> Innym sposobem utrwalania dzienników i innych danych wyjściowych zadań i zadań jest użycie biblioteki [konwencje plików wsadowych platformy Azure.](batch-task-output.md)
>
>

## <a name="job-preparation-task"></a>Zadanie przygotowania zadania


Przed wykonaniem zadań zadania batch wykonuje zadanie przygotowania zadania w każdym węźle obliczeniowym zaplanowanym do uruchomienia zadania. Domyślnie usługa Batch czeka na wykonanie zadania przygotowania zadania przed uruchomieniem zadań zaplanowanych do wykonania w węźle. Można jednak skonfigurować usługę, aby nie czekać. Jeśli węzeł zostanie ponownie uruchomiony, zadanie przygotowania zadania zostanie uruchomione ponownie. Można również wyłączyć to zachowanie. Jeśli masz zadanie z zadaniem przygotowania zadania i skonfigurowanym zadaniem menedżera zadań, zadanie przygotowania zadania jest uruchamiane przed zadaniem menedżera zadań, podobnie jak w przypadku wszystkich innych zadań. Zadanie przygotowania zadania jest zawsze uruchamiane jako pierwsze.

Zadanie przygotowania zadania jest wykonywane tylko w węzłach, które są zaplanowane do uruchomienia zadania. Zapobiega to niepotrzebnemu wykonaniu zadania przygotowania w przypadku, gdy węzeł nie jest przypisany zadanie. Może to nastąpić, gdy liczba zadań dla zadania jest mniejsza niż liczba węzłów w puli. Ma również zastosowanie, gdy [jednocześnie wykonywanie zadań](batch-parallel-node-tasks.md) jest włączone, co pozostawia niektóre węzły bezczynności, jeśli liczba zadań jest niższa niż całkowita możliwych równoczesnych zadań. Nie uruchamiając zadania przygotowania zadania w bezczynnych węzłach, można wydać mniej pieniędzy na opłaty za transfer danych.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] różni się od [CloudPool.StartTask][pool_starttask] w tym JobPreparationTask wykonuje na początku każdego zadania, natomiast StartTask wykonuje tylko wtedy, gdy węzeł obliczeniowy najpierw dołącza do puli lub restartuje.
>


>## <a name="job-release-task"></a>Zadanie zwolnienia z pracy

Gdy zadanie jest oznaczone jako ukończone, zadanie zwalniania zadania jest wykonywane w każdym węźle w puli, która wykonała co najmniej jedno zadanie. Oznaczysz zadanie jako ukończone, wystawiając żądanie zakończenia. Usługa wsadowa następnie ustawia stan zadania na *kończący,* kończy wszystkie aktywne lub uruchomione zadania skojarzone z zadaniem i uruchamia zadanie zwolnienia zlecenia. Zadanie następnie przenosi się do stanu *ukończone.*

> [!NOTE]
> Usunięcie zadania wykonuje również zadanie zwalniania zadania. Jeśli jednak zadanie zostało już zakończone, zadanie zwalniające nie zostanie uruchomione po raz drugi, jeśli zadanie zostanie później usunięte.

Zadania zwalniania zadań można uruchomić przez maksymalnie 15 minut przed zakończeniem przez usługę Batch. Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną interfejsu API REST](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Zadania przygotowania i zwalniania zadań za pomocą usługi Batch .NET
Aby użyć zadania przygotowania zadania, przypisz [obiekt JobPreparationTask][net_job_prep] do właściwości [CloudJob.JobPreparationTask][net_job_prep_cloudjob] zadania zadania. Podobnie zainicjować [JobReleaseTask][net_job_release] i przypisać go do zadania [CloudJob.JobReleaseTask][net_job_prep_cloudjob] właściwości, aby ustawić zadanie zwalniania zadania.

W tym fragmentie kodu `myBatchClient` jest wystąpieniem [BatchClient][net_batch_client]i `myPool` jest istniejącą pulą w ramach konta Batch.

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

Jak wspomniano wcześniej, zadanie wydania jest wykonywane po zakończeniu lub usunięciu zadania. Zakończ zadanie z [JobOperations.TerminateJobAsync][net_job_terminate]. Usuń zadanie z [JobOperations.DeleteJobAsync][net_job_delete]. Zazwyczaj kończy się lub usuwa zadanie po zakończeniu jego zadań lub po osiągnięciu limitu czasu, który został osiągnięty.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Przykładowy kod w usłudze GitHub
Aby wyświetlić zadania przygotowania i zwalniania zadania w akcji, sprawdź [JobPrepRelease][job_prep_release_sample] przykładowy projekt na GitHub. Ta aplikacja konsoli wykonuje następujące czynności:

1. Tworzy pulę z dwoma węzłami.
2. Tworzy zadanie z przygotowaniem zadania, zwolnieniem i zadaniami standardowymi.
3. Uruchamia zadanie przygotowania zadania, które najpierw zapisuje identyfikator węzła w pliku tekstowym w katalogu "udostępnionym" węzła.
4. Uruchamia zadanie w każdym węźle, który zapisuje swój identyfikator zadania w tym samym pliku tekstowym.
5. Po zakończeniu wszystkich zadań (lub osiągnięciu limitu czasu) zawartość pliku tekstowego każdego węzła jest drukowana na konsoli.
6. Po zakończeniu zadania uruchamia zadanie zwalniania zadania, aby usunąć plik z węzła.
7. Drukuje kody zakończenia zadania przygotowania i zwalniania dla każdego węzła, na którym zostały wykonane.
8. Wstrzymuje wykonywanie, aby umożliwić potwierdzenie usunięcia zadania i/lub puli.

Dane wyjściowe z przykładowej aplikacji są podobne do następujących:

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
> Ze względu na zmienną tworzenia i czasu rozpoczęcia węzłów w nowej puli (niektóre węzły są gotowe do zadań przed innymi), mogą być widoczne różne dane wyjściowe. W szczególności, ponieważ zadania są wykonywane szybko, jeden z węzłów puli może wykonać wszystkie zadania zadania. W takim przypadku można zauważyć, że zadania przygotowania i zwolnienia zadania nie istnieją dla węzła, który wykonał żadnych zadań.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Sprawdzanie zadań związanych z przygotowaniem i zwalnianiem zadań w witrynie Azure portal
Po uruchomieniu przykładowej aplikacji można użyć [witryny Azure Portal,][portal] aby wyświetlić właściwości zadania i jego zadań, a nawet pobrać udostępniony plik tekstowy, który jest modyfikowany przez zadania zadania.

Poniższy zrzut ekranu przedstawia **blok zadania przygotowania** w witrynie Azure portal po uruchomieniu przykładowej aplikacji. Przejdź do *właściwości JobPrepReleaseSampleJob* po zakończeniu zadań (ale przed usunięciem zadania i puli) i kliknij przycisk **Przygotowanie zadań** lub **Zwolnij zadania,** aby wyświetlić ich właściwości.

![Właściwości przygotowania zadania w witrynie Azure portal][1]

## <a name="next-steps"></a>Następne kroki
### <a name="application-packages"></a>Pakiety aplikacji
Oprócz zadania przygotowania zadania można również użyć funkcji [pakietów aplikacji](batch-application-packages.md) usługi Batch do przygotowania węzłów obliczeniowych do wykonania zadania. Ta funkcja jest szczególnie przydatna do wdrażania aplikacji, które nie wymagają uruchamiania instalatora, aplikacji zawierających wiele plików (100+) lub aplikacji, które wymagają ścisłej kontroli wersji.

### <a name="installing-applications-and-staging-data"></a>Instalowanie aplikacji i danych przemieszczania
Ten wpis na forum MSDN zawiera omówienie kilku metod przygotowywania węzłów do uruchamiania zadań:

[Instalowanie aplikacji i danych przemieszczania w węzłach obliczeniowych wsadowych][forum_post]

Napisany przez jednego z członków zespołu usługi Azure Batch, omówiono kilka technik, których można użyć do wdrażania aplikacji i danych do obliczeń węzłów.

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
