---
title: Planowanie zadań usługi Azure Container Registry
description: Ustawianie czasomierzy, aby uruchomić zadanie usługi Azure Container Registry zgodnie ze zdefiniowanym harmonogramem.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509705"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Uruchamianie zadania usługi ACR zgodnie ze zdefiniowanym harmonogramem

W tym artykule dowiesz się, jak uruchomić [zadań ACR](container-registry-tasks-overview.md) zgodnie z harmonogramem. Zaplanować zadanie, konfigurując co najmniej jeden *wyzwalaczy czasomierza*. 

Planowanie zadania jest przydatne w przypadku takich scenariuszy jak poniższe:

* Uruchamianie obciążenia kontenera dla operacji zaplanowanej konserwacji. Na przykład uruchamianie konteneryzowanych aplikacji, aby usunąć niepotrzebne obrazów z rejestru.
* Uruchamianie zestawu testów na obrazie produkcji podczas pracy w ramach monitorowania działającej witryny.

Aby uruchomić przykłady w tym artykule, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wersja 2.0.68 lub nowszy jest wymagany. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Planowanie zadań — informacje

* **Wyzwalacz za pomocą wyrażenia cron** -korzysta z wyzwalacza czasomierza zadania *wyrażenie cron*. Wyrażenie jest ciąg zawierający pięć pól, określając minuty, godziny, dnia, miesiąca i dzień tygodnia, aby wyzwolić zadanie. Obsługiwane są maksymalnie jeden raz na minutę częstotliwości. 

  Na przykład, wyrażenie `"0 12 * * Mon-Fri"` wyzwala zadanie w południe UTC na każdy dzień tygodnia. Zobacz [szczegóły](#cron-expressions) w dalszej części tego artykułu.
* **Wiele wyzwalaczy czasomierza** — Dodawanie wielu czasomierzy do zadania jest dozwolony, tak długo, jak różne harmonogramy. 
    * Podczas tworzenia zadania, lub je dodać później, należy określić wiele wyzwalaczy czasomierza.
    * Opcjonalnie nazwij wyzwalacze, aby ułatwić zarządzanie lub zadania rejestru Azure container Registry zapewnia domyślne nazwy wyzwalacza.
    * Jeśli harmonogramy czasomierza nakładają się w czasie, zadania ACR wyzwala zadanie w zaplanowanym czasie dla każdego czasomierza. 
* **Inne wyzwalacze zadań** — w ramach zadania wyzwalanej przez czasomierz można również włączyć wyzwalacze w oparciu [zatwierdzenia kodu źródłowego](container-registry-tutorial-build-task.md) lub [podstawowa aktualizacji obrazu](container-registry-tutorial-base-image-update.md). Podobnie jak inne zadania ACR można również [ręcznie wyzwolić][az-acr-task-run] zaplanowanego zadania.

## <a name="create-a-task-with-a-timer-trigger"></a>Utwórz zadanie z wyzwalaczem czasomierza

Po utworzeniu zadania z [az acr zadanie Tworzenie][az-acr-task-create] polecenia, można opcjonalnie dodać wyzwalacza czasomierza. Dodaj `--schedule` parametru i przekazać wyrażenie narzędzia cron dla czasomierza. 

Jako prosty przykład następujące polecenie uruchamiania wyzwalaczy `hello-world` obrazu z usługi Docker Hub codziennie o godzinie 21:00 czasu UTC. Zadanie jest uruchamiane bez kontekst kodu źródłowego.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Uruchom [az acr zadanie Pokaż][az-acr-task-show] polecenie, aby zobaczyć, że skonfigurowano wyzwalacza czasomierza. Domyślnie jest także możliwe wyzwalacza aktualizacji obrazu podstawowego.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Wyzwól zadanie ręcznie za pomocą [az acr zadania][az-acr-task-run] aby upewnić się, że jej jest prawidłowo skonfigurowane:

```azurecli
az acr task run --name mytask --registry myregistry
```

Jeśli kontener jest uruchomiony pomyślnie, dane wyjściowe będą podobne do następujących:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Po upływie zaplanowanego czasu uruchomienia [az acr zadań listy — uruchamia][az-acr-task-list-runs] polecenie, aby sprawdzić, czy czasomierz wyzwolenie zadania, zgodnie z oczekiwaniami:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Po pomyślnym czasomierza dane wyjściowe będą podobne do następujących:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Zarządzanie wyzwalaczy czasomierza

Użyj [az acr zadanie czasomierza][az-acr-task-timer] polecenia do zarządzania wyzwalaczy czasomierza zadania usługi ACR.

### <a name="add-or-update-a-timer-trigger"></a>Dodawanie lub aktualizowanie wyzwalacz czasomierza

Po utworzeniu zadania przy użyciu opcjonalnie Dodaj wyzwalacz czasomierza [Dodaj az acr zadanie czasomierza][az-acr-task-timer-add] polecenia. Poniższy przykład dodaje nazwę wyzwalacza czasomierza *czasomierz 2* do *mytask* utworzony wcześniej. Ten czasomierz wyzwala zadanie codziennie o godzinie 10:30 czasu UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Zaktualizuj harmonogram istniejącego wyzwalacza lub zmienić jego stan za pomocą [az acr zadanie czasomierza aktualizacji][az-acr-task-timer-update] polecenia. Na przykład aktualizacji wyzwalacza o nazwie *czasomierz 2* do wyzwalania zadania o 11:30 czasu UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista wyzwalaczy czasomierza

[Az acr zadanie czasomierza listy][az-acr-task-timer-list] polecenie wyświetla wyzwalaczy czasomierza, skonfiguruj dla zadania:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Przykładowe dane wyjściowe:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Usuń wyzwalacz czasomierza 

Użyj [az acr zadanie czasomierza Usuń][az-acr-task-timer-remove] polecenie, aby usunąć z wyzwalacza czasomierza z zadania. Poniższy przykład usuwa *czasomierz 2* wyzwalającej wydanie na podstawie *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Wyrażenia cron

Używa zadania ACR [NCronTab](https://github.com/atifaziz/NCrontab) biblioteki interpretowanie wyrażeń cron. Obsługiwane wyrażenia w zadaniach usługi ACR mają pięć pól wymaganych rozdzielane średnikami biały:

`{minute} {hour} {day} {month} {day-of-week}`

Strefę czasową, używaną za pomocą wyrażeń cron jest uniwersalny czas koordynowany (UTC). Godziny są w formacie 24-godzinnym.

> [!NOTE]
> Nie obsługuje zadania ACR `{second}` lub `{year}` pole wyrażeń cron. Jeśli kopiujesz wyrażenie cron, używane w innym systemie, należy usunąć te pola, jeśli są one używane.

Każde pole może mieć jedną z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>"5 * * * *"</nobr>|co godzinę w ciągu 5 minut po pełnej godzinie|
|Wszystkie wartości (`*`)|<nobr>"* 5 * * *"</nobr>|co minutę godziny początku 5:00 UTC (60 razy dziennie)|
|Zakres (`-` operatora)|<nobr>"0 1-3 * * *"</nobr>|3 razy dziennie, 1:00 2:00 i 3:00 UTC|  
|Zestaw wartości (`,` operatora)|<nobr>"20,30,40 * * * *"</nobr>|3 razy na godzinę, o 20 minut, 30 minut, a następnie 40 minut po pełnej godzinie|
|Wartość interwału (`/` operatora)|<nobr>"*/10 * * * *"</nobr>|6 razy na godzinę w ciągu 10 minut, 20 minut i tak dalej, po pełnej godzinie

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Przykłady usługi cron

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"*/5 * * * *"`|co pięć minut|
|`"0 * * * *"`|jeden raz w górnej części co godzinę|
|`"0 */2 * * *"`|co dwie godziny|
|`"0 9-17 * * *"`|co godzinę od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|o 9:30 czasu UTC, każdego dnia|
|`"30 9 * * 1-5"`|o 9:30 w każdy dzień tygodnia w formacie UTC|
|`"30 9 * Jan Mon"`|o 9:30 w każdy poniedziałek w styczniu w formacie UTC|


## <a name="next-steps"></a>Kolejne kroki

Dla przykłady zadań wyzwalanych zatwierdzenia kodu źródłowego lub obrazu podstawowego aktualizacji, zapoznaj się z [serii samouczków zadania ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli