---
title: Utwórz zadania przygotowanie zadania i zakończenie zadania na węzłach obliczeniowych - partii zadań Azure | Dokumentacja firmy Microsoft
description: Użyj poziom zadania przygotowanie zadania, aby zminimalizować transfer danych w węzłach obliczeniowych partii zadań Azure, a następnie zwolnij zadania oczyszczania węzła po zakończeniu zadania.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 543c03c22b31389c3d6e048cc9f13c24add5aae7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Uruchom zadanie przygotowanie i wersji zadania w partii węzły obliczeniowe

 Zadanie usługi partia zadań Azure często wymaga pewnej formy instalacji, przed jego zadania są wykonywane, a po zadania konserwacji, po zakończeniu jego zadań podrzędnych. Może być konieczne pobranie typowych zadań, danych wejściowych do węzłów obliczeniowych lub przekazać dane wyjściowe zadania do magazynu Azure po zakończeniu zadania. Można użyć **zadania przygotowanie** i **zadania wersji** zadania do wykonania tych operacji.

## <a name="what-are-job-preparation-and-release-tasks"></a>Co to są zadanie przygotowanie i zwolnij zadania?
Przed uruchomieniem zadania, zadanie przygotowanie zadania działa we wszystkich węzłach obliczeniowych zaplanowane co najmniej jedno zadanie. Po zakończeniu zadania zadania Zwolnienie zadania działa na każdym węźle w puli, która jest wykonywana co najmniej jedno zadanie. Podobnie jak w przypadku normalnych partii zadań, można określić w wierszu polecenia można wywołać podczas wykonywania zadania zadanie przygotowanie lub wersji.

Zadania przygotowania i wersji oferują znanych partii zadań funkcje, takie jak pobieranie pliku ([pliki zasobów][net_job_prep_resourcefiles]), z podwyższonym poziomem uprawnień wykonywania, zmienne środowiskowe niestandardowych wykonywania maksymalny czas trwania, liczbę ponownych prób i czas przechowywania pliku.

W poniższych sekcjach omówiono sposób użycia [JobPreparationTask] [ net_job_prep] i [JobReleaseTask] [ net_job_release] klasy znalezione w [partiami platformy .NET] [ api_net] biblioteki.

> [!TIP]
> Przygotowanie i wersji zadania są szczególnie przydatne w środowiskach "udostępniania puli", w którym puli węzłów obliczeniowych będzie się powtarzał między uruchomionych zadań i jest używany przez wiele zadań.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kiedy zadanie przygotowanie i zwolnić zadania
Przygotowanie zadania i wersji zadania są świetnie sprawdza się w następujących sytuacjach:

**Pobierz dane typowe zadania**

Zadania wsadowe często wymagają ze wspólnego zestawu danych jako dane wejściowe dla zadania. Na przykład w obliczeniach codzienne analizy ryzyka, danych rynkowych jest określonych zadań, jeszcze wspólne dla wszystkich zadań w zadaniu. Tych danych rynkowych, rozmiar, często kilku gigabajtów powinien zostać pobrany w każdym węźle obliczeń tylko raz, aby można go używać dowolnego zadania w węźle. Użyj **zadanie przygotowanie zadania** do pobrania tych danych do każdego węzła przed wykonywanie zadania innych zadań.

**Usuń dane wyjściowe poleceń i zadań**

W środowisku "udostępniania puli", gdzie węzły obliczeniowe puli nie są wycofany z eksploatacji między zadaniami, może być konieczne usunięcie danych zadania między działa. Może być konieczne, co pozwala zaoszczędzić miejsce na dysku w węzłach lub spełnia zasady zabezpieczeń w organizacji. Użyj **zadania Zwolnienie zadania** do usuwania danych, która została pobrana przez zadanie przygotowanie zadania lub generowane podczas wykonywania zadania.

**Przechowywanie dziennika**

Warto przechowywać kopię plików dziennika, które generują zadań lub może ulec awarii pliki zrzutu, które mogą być generowane przez aplikacje nie powiodło się. Użyj **zadania Zwolnienie zadania** w takich przypadkach podczas kompresji i przekazywanie tych danych na [usługi Azure Storage] [ azure_storage] konta.

> [!TIP]
> Inny sposób, aby utrwalić dzienniki i innych poleceń i zadań output danych jest użycie [konwencje pliku wsadowego Azure](batch-task-output.md) biblioteki.
> 
> 

## <a name="job-preparation-task"></a>Zadanie przygotowania zadania
Przed wykonaniem zadania wsadowego wykonuje zadanie przygotowanie zadania w każdym węźle obliczeń, który jest zaplanowane do uruchomienia zadania. Domyślnie usługa partia zadań czeka na zadanie przygotowanie zadania należy wykonać przed uruchomieniem zadania zaplanowane do wykonania na węźle. Można jednak skonfigurować usługę nie chcesz czekać. Jeśli węzeł zostanie uruchomiony ponownie, zostanie ponownie uruchomione zadanie przygotowanie zadania, ale można również wyłączyć to zachowanie.

Zadanie przygotowanie zadania jest wykonywane tylko w przypadku węzłów, które są zaplanowane do uruchomienia zadania. Zapobiega to niepotrzebnych wykonywanie przygotowanie zadania, w przypadku, gdy węzeł nie jest przypisany zadania. Taka sytuacja może wystąpić, gdy liczba zadań dla zadania jest mniejsza niż liczba węzłów w puli. Ma również zastosowanie podczas [wykonywanie zadań jednoczesnych](batch-parallel-node-tasks.md) jest włączony, dlatego jeśli bezczynności niektóre węzły liczby zadań jest niższa niż całkowita liczba zadań jednoczesnych możliwe. Przez nie uruchomione zadanie przygotowanie zadania w węzłach bezczynności, możesz można kupować mniej opłat za transfer danych.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] różni się od [CloudPool.StartTask] [ pool_starttask] w tym JobPreparationTask wykonuje się na początku każdego zadania, natomiast StartTask wykonuje, tylko gdy węzeł obliczeniowy najpierw dołącza puli lub ponownego uruchomienia.
> 
> 

## <a name="job-release-task"></a>Zadanie zwolnienia zadania
Gdy zadanie jest oznaczony jako ukończone, zadanie zwolnienie zadania jest wykonywany w każdym węźle w puli, która jest wykonywana co najmniej jedno zadanie. Zadania są oznaczone jako ukończone, wysyłając żądanie przerwania. Następnie usługa partia zadań ustawia stan zadania na *przerywanie*kończy żadnych zadań aktywnych lub nie działają, skojarzone z zadaniem i uruchamia zadanie zwolnienie zadania. Zadanie zostaje następnie przeniesiona do *ukończone* stanu.

> [!NOTE]
> Usuwanie zadania wykonuje również zadania Zwolnienie zadania. Jednak jeśli zadanie zostało zakończone, zwolnienie zadania nie uruchomieniu po raz drugi Jeśli zadanie później zostanie usunięty.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Zadania w środowisku przedprodukcyjnym i zwolnij zadania związane z partiami platformy .NET
Aby użyć zadania przygotowanie zadania, Przypisz [JobPreparationTask] [ net_job_prep] obiektu z zadania [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] właściwości. Podobnie, zainicjować [JobReleaseTask] [ net_job_release] i przypisz go do Twojego zadania [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] właściwości można ustawić zadanie zwolnienie zadania.

W poniższym przykładzie `myBatchClient` jest wystąpieniem [BatchClient][net_batch_client], i `myPool` jest istniejącej puli w ramach konta usługi partia zadań.

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

Jak wspomniano wcześniej, zwolnienie zadania jest wykonywany, gdy zadanie zostanie przerwane lub usunięty. Zakończ zadanie z [JobOperations.TerminateJobAsync][net_job_terminate]. Usuń zadanie o [JobOperations.DeleteJobAsync][net_job_delete]. Zwykle przerwanie lub usunąć zadania po zakończeniu jego zadań podrzędnych lub gdy został osiągnięty limit czasu, który został zdefiniowany.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Przykładowy kod w witrynie GitHub
Zobacz Przygotowanie zadania i zwolnienie zadania w akcji, zapoznaj się [JobPrepRelease] [ job_prep_release_sample] przykładowy projekt w witrynie GitHub. Ta aplikacja konsoli wykonuje następujące czynności:

1. Tworzona jest pula z dwoma węzłami "małe".
2. Tworzy zadanie przygotowanie zadania, wersji i zadań standardowych.
3. Uruchamia zadanie przygotowanie zadania, który najpierw zapisuje identyfikator węzła do pliku tekstowego w katalogu "udostępniony" węzła.
4. Uruchamia zadanie, na każdym węźle, który zapisuje Identyfikatora zadania do tego samego pliku tekstowego.
5. Po wykonaniu wszystkich zadań (lub osiągnięto limit czasu), wyświetla zawartość pliku tekstowego każdego węzła do konsoli.
6. Po zakończeniu zadania uruchamia zadanie zwolnienie zadania do usunięcia pliku z węzła.
7. Drukuje kodów zakończenia zadania przygotowanie i wersji zadań dla każdego węzła, na którym są wykonywane.
8. Wstrzymuje wykonywanie umożliwia potwierdzenie usunięcia zadania i/lub puli.

Dane wyjściowe z przykładowej aplikacji są podobne do następujących:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
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
> Z powodu zmiennej tworzenia i uruchamiania czasu węzłów w nowej puli (niektóre węzły są gotowe do zadań przed innymi) mogą pojawić się różne wyniki. W szczególności ponieważ zadania są wykonywane szybko, jednym z węzłów w puli może wykonywać wszystkie zadania podrzędne zadania. W takim przypadku można zauważyć, że zadanie przygotowywanie i wersji zadania nie istnieją dla węzła, który wykonać żadnych zadań.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Przejrzyj zadanie przygotowanie i wersji zadania w portalu Azure
Po uruchomieniu aplikacji przykładowej, można użyć [portalu Azure] [ portal] wyświetlania właściwości zadania i jego zadań podrzędnych i nawet pobierania pliku tekstowego udostępnionego, który jest modyfikowany przez zadania.

Poniżej przedstawiono zrzut ekranu **przygotowanie zadania bloku** w portalu Azure po uruchomieniu aplikacji przykładowej. Przejdź do *JobPrepReleaseSampleJob* właściwości po zakończeniu zadania (ale przed usunięciem zadania, a pula) i kliknij przycisk **zadań przygotowawczych** lub **wersji zadania** do przeglądania właściwości.

![Właściwości przygotowanie zadania w portalu Azure][1]

## <a name="next-steps"></a>Kolejne kroki
### <a name="application-packages"></a>Pakiety aplikacji
Oprócz zadanie przygotowanie zadania, można również użyć [pakietów aplikacji](batch-application-packages.md) funkcji partii do przygotowania do wykonania zadań węzłów obliczeniowych. Ta funkcja jest szczególnie przydatna w przypadku wdrażania aplikacji, które nie wymagają uruchomiony Instalator, aplikacje, które zawierają wiele plików (100 +) lub aplikacji, które wymagają kontroli wersji strict.

### <a name="installing-applications-and-staging-data"></a>Instalowanie aplikacji i danych na potrzeby przemieszczania
Ten wpis na forum MSDN zawiera omówienie kilka metod przygotowywania węzły do uruchamiania zadań:

[Instalowanie aplikacji i danych na partii przemieszczania węzły obliczeniowe][forum_post]

Napisane przez jeden z członków zespołu partii zadań Azure, opisano kilka metod, co umożliwia wdrażanie aplikacji i danych, aby węzły obliczeniowe.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
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
