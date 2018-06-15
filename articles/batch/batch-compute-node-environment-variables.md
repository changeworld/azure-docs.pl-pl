---
title: Partia zadań Azure obliczeniowe zmiennych środowiskowych węzła | Dokumentacja firmy Microsoft
description: Obliczenia bazy danych odwołanie do zmiennej środowiskowej węzła dla analizach wsadowych Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: danlep
ms.openlocfilehash: ca8d6a6484cd1f145e7d807681bf2d012f2399e0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312705"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Zmienne środowiskowe węzła obliczeń platformy Azure partii
[Usługi partia zadań Azure](https://azure.microsoft.com/services/batch/) ustawia następujące zmienne środowiskowe w węzłach obliczeniowych. Możesz odwoływać się do tych zmiennych środowiskowych w wierszy polecenia zadania oraz programy i skrypty uruchamiane przez wiersze polecenia.

Aby uzyskać dodatkowe informacje dotyczące używania zmiennych środowiskowych z partii, zobacz [ustawienia środowiska dla zadań](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Widoczność zmiennej środowiskowej

Te zmienne środowiskowe są widoczne tylko w kontekście **użytkownika zadań**, konto użytkownika w węźle, w którym jest wykonywane zadania. Będą one *niewidoczne*, jeśli [zdalnie połączysz się](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) z węzłem obliczeniowym, za pomocą protokołu RDP lub SSH i wyświetlisz listę zmiennych środowiskowych. Dzieje się tak dlatego, że konto użytkownika, używane na potrzeby połączenia zdalnego jest inne niż konto używane przez zadanie podrzędne.

## <a name="command-line-expansion-of-environment-variables"></a>Rozszerzanie zmiennych środowiskowych wiersza polecenia

Wiersze polecenia wykonywane zadania na obliczeniowe węzłów nie uruchamiaj w powłoce. W związku z tym te wiersze polecenia nie można natywnie skorzystać z funkcji powłoki, takich jak rozszerzanie zmiennych środowiskowych (dotyczy to również `PATH`). Aby móc korzystać z takich funkcji, należy **wywołania powłoki** w wierszu polecenia. Na przykład uruchom `cmd.exe` w systemie Windows węzły obliczeniowe lub `/bin/sh` w węzłach Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Zmienne środowiskowe

| Nazwa zmiennej                     | Opis                                                              | Dostępność | Przykład |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Nazwa konta usługi partia zadań, należącego do zadania.                  | Wszystkie zadania.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | Katalog w [katalog roboczy zadania] [ files_dirs] węzły obliczeniowe, w którym certyfikaty są przechowywane w systemie Linux. Węzły obliczeniowe należy pamiętać, że ta zmienna środowiskowa nie ma zastosowania do systemu Windows.                                                  | Wszystkie zadania.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Identyfikator zadania, do którego należy zadanie podrzędne. | Wszystkie zadania z wyjątkiem uruchomienia zadania. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Pełna ścieżka zadanie przygotowanie [katalogu zadania] [ files_dirs] w węźle. | Wszystkie zadania z wyjątkiem uruchamiania zadań i zadania przygotowanie zadania. Dostępne tylko, gdy zadanie jest konfigurowana zadanie przygotowanie zadania. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Pełna ścieżka zadanie przygotowanie [katalog roboczy zadania] [ files_dirs] w węźle. | Wszystkie zadania z wyjątkiem uruchamiania zadań i zadania przygotowanie zadania. Dostępne tylko, gdy zadanie jest konfigurowana zadanie przygotowanie zadania. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | Identyfikator węzła, przypisana do zadania. | Wszystkie zadania. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Pełna ścieżka katalogu głównego wszystkich [partii katalogów] [ files_dirs] w węźle. | Wszystkie zadania. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Pełna ścieżka [udostępnionego katalogu] [ files_dirs] w węźle. Wszystkie zadania, które są wykonywane w węźle ma dostęp do odczytu i zapisu do tego katalogu. Zadania, które są wykonywane na innych węzłach nie mieć zdalnego dostępu do tego katalogu (nie jest on katalogu sieciowym "udostępniony"). | Wszystkie zadania. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Pełna ścieżka [uruchomić zadania katalogu] [ files_dirs] w węźle. | Wszystkie zadania. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Identyfikator puli, w której jest uruchamiane zadanie podrzędne. | Wszystkie zadania. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Pełna ścieżka [katalogu zadania] [ files_dirs] w węźle. Ten katalog zawiera `stdout.txt` i `stderr.txt` zadania i AZ_BATCH_TASK_WORKING_DIR. | Wszystkie zadania. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Identyfikator bieżącego zadania. | Wszystkie zadania z wyjątkiem uruchomienia zadania. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Pełna ścieżka [katalog roboczy zadania] [ files_dirs] w węźle. Aktualnie uruchomione zadanie ma dostęp do odczytu i zapisu do tego katalogu. | Wszystkie zadania. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Lista węzłów i liczba rdzeni przypadająca na węzeł, który jest przydzielony do [zadań wielowystąpieniowy][multi_instance]. Węzły i rdzeni są wyświetlane w formacie `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, gdy liczba węzłów następuje co najmniej jeden adres IP węzła i liczba rdzeni dla każdego. |  Mająca wiele wystąpień podstawowym i podzadania. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Listy węzłów, które są przydzielone do [zadań wielowystąpieniowy] [ multi_instance] w formacie `nodeIP;nodeIP`. | Mająca wiele wystąpień podstawowym i podzadania. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Listy węzłów, które są przydzielone do [zadań wielowystąpieniowy] [ multi_instance] w formacie `nodeIP,nodeIP`. | Mająca wiele wystąpień podstawowym i podzadania. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | Adres IP i port węźle obliczeń, w którym głównym zadaniem [zadań wielowystąpieniowy] [ multi_instance] działa. | Mająca wiele wystąpień podstawowym i podzadania. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Ścieżka katalogu identyczną zadania głównej i każdy podzadaniem [zadań wielu wystąpieniach][multi_instance]. Ścieżka istnieje w każdym węźle, na którym wielu wystąpieniach zadanie działa i jest dostępny dla polecenia zadania uruchomione w tym węźle odczytu/zapisu (zarówno [polecenia koordynacji] [ coord_cmd] i [polecenia aplikacji][app_cmd]). Podzadań lub podstawowe zadania, które wykonania na innych węzłach nie mieć zdalnego dostępu do tego katalogu (nie jest on katalogu sieciowym "udostępniony"). | Mająca wiele wystąpień podstawowym i podzadania. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Określa, czy bieżący węzeł główny węzeł [zadań wielowystąpieniowy][multi_instance]. Możliwe wartości to `true` i `false`.| Mająca wiele wystąpień podstawowym i podzadania. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Jeśli `true`, bieżący węzeł jest węzłem dedykowanych. Jeśli `false`, jest [węzła niskiego priorytetu](batch-low-pri-vms.md). | Wszystkie zadania. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
