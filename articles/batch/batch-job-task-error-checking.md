---
title: Sprawdzanie błędów zadań i zadań — usługa Azure Batch | Dokumenty firmy Microsoft
description: Błędy do sprawdzania zadań i zadań rozwiązywania problemów
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087016"
---
# <a name="job-and-task-error-checking"></a>Sprawdzanie błędów zadania i zadania

Istnieją różne błędy, które mogą wystąpić podczas dodawania zadań i zadań. Wykrywanie błędów dla tych operacji jest proste, ponieważ wszelkie błędy są zwracane natychmiast przez interfejs API, interfejs wiersza polecenia lub interfejsu użytkownika.  Jednak istnieją błędy, które mogą się zdarzyć później, gdy zadania i zadania są zaplanowane i uruchomione.

W tym artykule opisano błędy, które mogą wystąpić po przesłaniu zadań i zadań. Wyświetla listę i wyjaśnia błędy, które muszą być sprawdzane i obsługiwane.

## <a name="jobs"></a>Stanowiska

Zadanie to grupowanie jednego lub więcej zadań, a zadania faktycznie określają wiersze polecenia do uruchomienia.

Podczas dodawania zadania można określić następujące parametry, które mogą mieć wpływ na sposób, w jaki zadanie może zakończyć się niepowodzeniem:

- [Ograniczenia zatrudnienia](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Właściwość `maxWallClockTime` można opcjonalnie określić, aby ustawić maksymalną ilość czasu zadanie może być aktywne lub uruchomione. Jeśli zostanie przekroczona, zadanie `terminateReason` zostanie zakończone z właściwością ustawioną w [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) dla zadania.
- [Zadanie przygotowania zadania](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Jeśli określono, zadanie przygotowania zadania jest uruchamiane po raz pierwszy zadanie jest uruchamiane dla zadania w węźle. Zadanie przygotowania zadania może zakończyć się niepowodzeniem, co doprowadzi do nieubiegania zadania i niekończenia zadania.
- [Zadanie zwolnienia zadania](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Zadanie zwolnienia zadania można określić tylko wtedy, gdy skonfigurowano zadanie przygotowania zadania. Po zakończeniu zadania zadanie zwalniające zadanie jest uruchamiane w każdym z węzłów puli, w których uruchomiono zadanie przygotowania zadania. Zadanie zwolnienia zadania może zakończyć się niepowodzeniem, `completed` ale zadanie nadal zostanie przeniesione do stanu.

### <a name="job-properties"></a>Właściwości zadania

Następujące właściwości zadania powinny być sprawdzane pod kątem błędów:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - Właściwość `terminateReason` może mieć wartości `maxWallClockTime`wskazujące, że , określone w ograniczeniach zadania, został przekroczony i w związku z tym zadanie zostało zakończone. Można również ustawić, aby wskazać zadanie nie `onTaskFailure` powiodło się, jeśli właściwość zadania została ustawiona odpowiednio.
  - Właściwość [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) jest ustawiona, jeśli wystąpił błąd planowania.
 
### <a name="job-preparation-tasks"></a>Zadania związane z przygotowaniem do pracy

Jeśli zadanie przygotowania zadania jest określone dla zadania, wystąpienie tego zadania zostanie uruchomione po raz pierwszy, gdy zadanie jest uruchamiane w węźle. Zadanie przygotowania zadania skonfigurowane w zadaniu można traktować jako szablon zadania, z wieloma wystąpieniami zadań przygotowania zadania są uruchamiane, aż do liczby węzłów w puli.

Wystąpienia zadań przygotowania zadania należy sprawdzić, aby ustalić, czy wystąpiły błędy:
- Po uruchomieniu zadania przygotowania zadania zadanie, a następnie zadanie, które [state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) wyzwoliło `preparing`zadanie przygotowania zadania zostanie przeniesione do stanu ; Jeśli zadanie przygotowania zadania zakończy się niepowodzeniem, zadanie `active` wyzwalające powróci do stanu i nie zostanie uruchomione.  
- Wszystkie wystąpienia zadania przygotowania zadania, które zostały uruchomione, można uzyskać z zadania za pomocą interfejsu API [przygotowanie listy i stan zadania wydania.](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) Podobnie jak w przypadku każdego zadania, dostępne `failureInfo`są `exitCode`informacje `result` [o wykonaniu](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) z właściwościami, takimi jak , i .
- Jeśli zadania przygotowania zadania nie powiodą się, zadania wyzwalania zadania nie zostaną uruchomione, zadanie nie zostanie ukończone i zostanie zablokowane. Pula może być niewykorzystana, jeśli nie ma innych zadań z zadaniami, które można zaplanować.

### <a name="job-release-tasks"></a>Zadania zwalniania zadań

Jeśli zadanie zwolnienia zadania jest określone dla zadania, a następnie po zakończeniu zadania wystąpienie zadania zwalniania zadania jest uruchamiane w każdym z węzłów puli, w którym zostało uruchomione zadanie przygotowania zadania.  Wystąpienia zadań zwalniania zadania powinny być sprawdzane, aby ustalić, czy wystąpiły błędy:
- Wszystkie wystąpienia uruchamianego zadania zwalniania zadania można uzyskać z zadania przy użyciu [stanu zadania przygotowywania listy](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)interfejsu API i wydania . Podobnie jak w przypadku każdego zadania, dostępne `failureInfo`są `exitCode`informacje `result` [o wykonaniu](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) z właściwościami, takimi jak , i .
- Jeśli jedno lub więcej zadań zwolnienia zadania zakończy się niepowodzeniem, `completed` zadanie nadal zostanie zakończone i przeniesione do stanu.

## <a name="tasks"></a>Zadania

Zadania zadania mogą zakończyć się niepowodzeniem z wielu powodów:

- Wiersz polecenia zadania kończy się niepowodzeniem, zwracając z kodem zakończenia niezerowego.
- Istnieją `resourceFiles` określone dla zadania, ale wystąpił błąd, który oznaczał, że jeden lub więcej plików nie zostało pobranych.
- Istnieją `outputFiles` określone dla zadania, ale wystąpił błąd, który oznaczał, że jeden lub więcej plików nie zostało przekazanych.
- Przekroczono czas, jaki upłynął `maxWallClockTime` dla zadania określonego przez właściwość w [ograniczeniach](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)zadania.

We wszystkich przypadkach należy sprawdzić następujące właściwości pod kątem błędów i informacji o błędach:
- Właściwość tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) zawiera wiele właściwości, które zawierają informacje o błędzie. [wynik](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) wskazuje, czy zadanie nie powiodło `exitCode` `failureInfo` się z jakiegokolwiek powodu, z i zapewniając więcej informacji o awarii.
- Zadanie zawsze zostanie przeniesione `completed` do [stanu](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), niezależnie od tego, czy się powiodło, czy nie.

Należy wziąć pod uwagę wpływ błędów zadań na zadanie i wszelkie zależności zadań.  [ExitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) Właściwość może być określona dla zadania, aby skonfigurować akcję dla zależności i dla zadania.
- W przypadku zależności [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) kontroluje, czy zadania zależne od zadania nie powiodło się są blokowane lub są uruchamiane.
- W przypadku zadania [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) określa, czy zadanie zakończone niepowodzeniem prowadzi do wyłączenia zadania, zakończenia lub pozostawienia go bez zmian.

### <a name="task-command-line-failures"></a>Błędy wiersza polecenia zadania

Po uruchomieniu wiersza polecenia zadania dane `stderr.txt` wyjściowe są zapisywane do i `stdout.txt`. Ponadto aplikacja może zapisywać pliki dziennika specyficzne dla aplikacji.

Jeśli węzeł puli, na którym zadanie zostało uruchomione nadal istnieje, pliki dziennika można uzyskać i wyświetlić. Na przykład portal Azure wyświetla listę i może wyświetlać pliki dziennika dla zadania lub węzła puli. Wiele interfejsów API umożliwia również wyświetlenie i uzyskanie plików zadań, takich jak [Pobierz z zadania](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Ze względu na pule i węzły puli często są efemeryczne, z węzłów stale dodawane i usuwane, a następnie zaleca się, że pliki dziennika są zachowywane. [Pliki wyjściowe zadań](https://docs.microsoft.com/azure/batch/batch-task-output-files) to wygodny sposób zapisywania plików dziennika w usłudze Azure Storage.

### <a name="output-file-failures"></a>Błędy plików wyjściowych
Przy każdym przekazywaniu pliku partia zapisuje dwa pliki `fileuploadout.txt` `fileuploaderr.txt`dziennika do węzła obliczeniowego i . Można sprawdzić te pliki dziennika, aby dowiedzieć się więcej o określonej awarii. W przypadkach, gdy nigdy nie podjęto próby przekazania pliku, na przykład dlatego, że samo zadanie nie może działać, te pliki dziennika nie będą istnieć.  

## <a name="next-steps"></a>Następne kroki

Sprawdź, czy aplikacja implementuje kompleksowe sprawdzanie błędów; może być bardzo ważne, aby szybko wykryć i zdiagnozować problemy.
