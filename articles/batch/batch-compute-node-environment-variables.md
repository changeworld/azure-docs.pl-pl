---
title: Zmienne środowiska wykonawczego zadania — usługa Azure Batch | Dokumenty firmy Microsoft
description: Wskazówki dotyczące zmiennych środowiska środowiska środowiska zadania i odwołanie dla usługi Azure Batch Analytics.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: ebaa06acf309a0f941b8b4efd76fa4e9e7460810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053946"
---
# <a name="azure-batch-runtime-environment-variables"></a>Zmienne środowiska wykonawczego usługi Azure Batch

[Usługa Azure Batch](https://azure.microsoft.com/services/batch/) ustawia następujące zmienne środowiskowe w węzłach obliczeniowych. Te zmienne środowiskowe można odwoływać się do tych zmiennych w wierszach poleceń zadania oraz w programach i skryptach uruchamianych przez wiersze polecenia.

Aby uzyskać więcej informacji na temat używania zmiennych środowiskowych z partią, zobacz [Ustawienia środowiska dla zadań](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Widoczność zmiennej środowiskowej

Te zmienne środowiskowe są widoczne tylko w kontekście **użytkownika zadania**, konta użytkownika w węźle, w którym wykonywane jest zadanie. Będą one *niewidoczne*, jeśli [zdalnie połączysz się](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) z węzłem obliczeniowym, za pomocą protokołu RDP lub SSH i wyświetlisz listę zmiennych środowiskowych. Dzieje się tak dlatego, że konto użytkownika, używane na potrzeby połączenia zdalnego jest inne niż konto używane przez zadanie podrzędne.

Aby uzyskać bieżącą wartość zmiennej `cmd.exe` środowiskowej, uruchom `/bin/sh` w węźle obliczeniowym systemu Windows lub w węźle systemu Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Rozszerzenie wiersza polecenia zmiennych środowiskowych

Wiersze poleceń wykonywane przez zadania w węzłach obliczeniowych nie są uruchamiane w powłoce. W związku z tym te wiersze poleceń nie mogą natywnie korzystać z funkcji powłoki, takich jak rozszerzenie zmiennej środowiskowej `PATH`(obejmuje to ). Aby skorzystać z takich funkcji, należy **wywołać powłokę** w wierszu polecenia. Na przykład `cmd.exe` uruchom w węzłach `/bin/sh` obliczeniowych systemu Windows lub w węzłach systemu Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Zmienne środowiskowe

| Nazwa zmiennej                     | Opis                                                              | Dostępność | Przykład |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Nazwa konta batch, do którego należy zadanie.                  | Wszystkie zadania.   | konto mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Adres URL konta batch. | Wszystkie zadania. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Prefiks wszystkich zmiennych środowiska pakietu aplikacji. Na przykład jeśli aplikacja "FOO" wersja "1" jest zainstalowany na puli, zmienna środowiskowa jest AZ_BATCH_APP_PACKAGE_FOO_1 (w systemie Linux) lub AZ_BATCH_APP_PACKAGE_FOO # 1 (w systemie Windows). AZ_BATCH_APP_PACKAGE_FOO_1 wskazuje lokalizację, w którą został pobrany pakiet (folder). Korzystając z domyślnej wersji pakietu aplikacji, należy użyć zmiennej środowiskowej AZ_BATCH_APP_PACKAGE bez numerów wersji. Jeśli w systemie Linux, a nazwa pakietu aplikacji jest "Agent-linux-x64", a wersja jest "1.1.46.0, nazwa środowiska jest rzeczywiście: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, przy użyciu podkreśleń i małe litery. Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications). | Dowolne zadanie ze skojarzonym pakietem aplikacji. Dostępne również dla wszystkich zadań, jeśli sam węzeł ma pakiety aplikacji. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) lub AZ_BATCH_APP_PACKAGE_FOO #1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Token uwierzytelniania, który udziela dostępu do ograniczonego zestawu operacji usługi Batch. Ta zmienna środowiskowa jest obecna tylko [wtedy, gdy uwierzytelnianieTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) są ustawiane po [dodaniu zadania.](/rest/api/batchservice/task/add#request-body) Wartość tokenu jest używana w interfejsach API usługi Batch jako poświadczenia do utworzenia klienta usługi Batch, na przykład w [interfejsie API batchclient.open().](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) | Wszystkie zadania. | Token dostępu OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Katalog w [katalogu roboczym zadań,][files_dirs] w którym certyfikaty są przechowywane dla węzłów obliczeniowych systemu Linux. Ta zmienna środowiskowa nie ma zastosowania do węzłów obliczeniowych systemu Windows.                                                  | Wszystkie zadania.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Lista węzłów, które są przydzielone do zadania `nodeIP,nodeIP` [wieloacenaturowego][multi_instance] w formacie . | Zadania podstawowe i podzadaniowe wielu wystąpień. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Określa, czy bieżący węzeł jest węzłem głównym [zadania z wieloma wystąpieniami][multi_instance]. Możliwe wartości `true` `false`są i .| Zadania podstawowe i podzadaniowe wielu wystąpień. | `true` |
| AZ_BATCH_JOB_ID                 | Identyfikator zadania, do którego należy zadanie podrzędne. | Wszystkie zadania z wyjątkiem zadania początkowego. | partiaj001 |
| AZ_BATCH_JOB_PREP_DIR           | Pełna ścieżka [katalogu zadań][files_dirs] przygotowania zadania w węźle. | Wszystkie zadania z wyjątkiem zadania początkowego i zadania przygotowania zadania. Dostępne tylko wtedy, gdy zadanie jest skonfigurowane z zadaniem przygotowania zadania. | C:\user\tasks\workitems\jobpreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Pełna ścieżka [katalogu roboczego zadania][files_dirs] przygotowania zadania w węźle. | Wszystkie zadania z wyjątkiem zadania początkowego i zadania przygotowania zadania. Dostępne tylko wtedy, gdy zadanie jest skonfigurowane z zadaniem przygotowania zadania. | C:\user\tasks\workitems\jobpreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Adres IP i port węzła obliczeniowego, na którym jest uruchamiane zadanie podstawowe [zadania z wieloma wystąpieniami.][multi_instance] | Zadania podstawowe i podzadaniowe wielu wystąpień. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Identyfikator węzła, do którego jest przypisane zadanie. | Wszystkie zadania. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Jeśli `true`bieżący węzeł jest dedykowanym węzłem. Jeśli `false`jest to [węzeł o niskim priorytecie](batch-low-pri-vms.md). | Wszystkie zadania. | `true` |
| AZ_BATCH_NODE_LIST              | Lista węzłów, które są przydzielone do zadania `nodeIP;nodeIP` [wieloacenaturowego][multi_instance] w formacie . | Zadania podstawowe i podzadaniowe wielu wystąpień. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Pełna ścieżka lokalizacji [instalacji systemu plików](virtual-file-mount.md) na poziomie węzła, w której znajdują się wszystkie katalogi instalacji. Udziały plików systemu Windows używają litery dysku, więc w przypadku systemu Windows dysk do instalacji jest częścią urządzeń i dysków.  |  Wszystkie zadania, w tym zadanie uruchamiania, mają dostęp do użytkownika, biorąc pod uwagę, że użytkownik jest świadomy uprawnień instalacji dla zainstalowanego katalogu. | Na przykład w Ubuntu lokalizacja to:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Pełna ścieżka katalogu głównego wszystkich [katalogów usługi Batch][files_dirs] w węźle. | Wszystkie zadania. | C:\użytkownik\zadania |
| AZ_BATCH_NODE_SHARED_DIR        | Pełna ścieżka [katalogu udostępnionego][files_dirs] w węźle. Wszystkie zadania wykonywane w węźle mają dostęp do odczytu/zapisu do tego katalogu. Zadania wykonywane w innych węzłach nie mają zdalnego dostępu do tego katalogu (nie jest to "udostępniony" katalog sieciowy). | Wszystkie zadania. | C:\użytkownik\zadania\udostępnione |
| AZ_BATCH_NODE_STARTUP_DIR       | Pełna ścieżka [katalogu zadań startowych][files_dirs] w węźle. | Wszystkie zadania. | C:\użytkownik\zadania\uruchamianie |
| AZ_BATCH_POOL_ID                | Identyfikator puli, w której jest uruchamiane zadanie podrzędne. | Wszystkie zadania. | numer y partii001 |
| AZ_BATCH_TASK_DIR               | Pełna ścieżka [katalogu zadań][files_dirs] w węźle. Ten katalog zawiera `stdout.txt` `stderr.txt` i dla zadania i AZ_BATCH_TASK_WORKING_DIR. | Wszystkie zadania. | C:\użytkownik\tasks\workitems\batchjob001\zadanie-1\task001 |
| AZ_BATCH_TASK_ID                | Identyfikator bieżącego zadania. | Wszystkie zadania z wyjątkiem zadania początkowego. | zadanie001 |
| AZ_BATCH_TASK_SHARED_DIR | Ścieżka katalogu identyczna dla zadania podstawowego i każdego podzadału [zadania z wieloma wystąpieniami][multi_instance]. Ścieżka istnieje w każdym węźle, na którym jest uruchamiane zadanie z wieloma wystąpieniami i jest odczytywana/zapisywana dla poleceń zadań uruchomionych w tym węźle (zarówno [polecenie koordynacji,][coord_cmd] jak i [polecenie aplikacji).][app_cmd] Podzadanie lub zadanie podstawowe wykonywane w innych węzłach nie ma zdalnego dostępu do tego katalogu (nie jest to "udostępniony" katalog sieciowy). | Zadania podstawowe i podzadaniowe wielu wystąpień. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Pełna ścieżka [katalogu roboczego zadania][files_dirs] w węźle. Aktualnie uruchomione zadanie ma dostęp do odczytu/zapisu do tego katalogu. | Wszystkie zadania. | C:\użytkownik\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Lista węzłów i liczba rdzeni na węzeł, które są przydzielone do [zadania wielostanowiskowego][multi_instance]. Węzły i rdzenie są wymienione w formacie`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, gdzie po liczbie węzłów następuje jeden lub więcej adresów IP węzła i liczba rdzeni dla każdego z nich. |  Zadania podstawowe i podzadaniowe wielu wystąpień. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
