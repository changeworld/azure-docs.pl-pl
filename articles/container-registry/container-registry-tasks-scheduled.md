---
title: Samouczek — planowanie zadania ACR
description: W tym samouczku dowiesz się, jak uruchomić zadanie rejestru kontenera platformy Azure zgodnie z określonym harmonogramem, ustawiając co najmniej jeden wyzwalacz czasomierza
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402882"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Uruchamianie zadania usługi ACR zgodnie ze zdefiniowanym harmonogramem

W tym samouczku pokazano, jak uruchomić [zadanie ACR](container-registry-tasks-overview.md) zgodnie z harmonogramem. Zaplanuj zadanie, konfigurując co najmniej jeden *wyzwalacz czasomierza*. Wyzwalacze czasomierza mogą być używane samodzielnie lub w połączeniu z innymi wyzwalaczami zadań.

W tym samouczku dowiesz się więcej o zadaniach planowania i:

> [!div class="checklist"]
> * Tworzenie zadania za pomocą wyzwalacza czasomierza
> * Zarządzanie wyzwalaczami czasomierza

Planowanie zadania jest przydatne w scenariuszach, takich jak:

* Uruchom obciążenie kontenera dla zaplanowanych operacji konserwacyjnych. Na przykład uruchom konteneryzowaną aplikację, aby usunąć niepotrzebne obrazy z rejestru.
* Uruchom zestaw testów na obrazie produkcyjnym w ciągu dnia pracy w ramach monitorowania witryny na żywo.

Aby uruchomić przykłady w tym artykule, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.68 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Planowanie zadania — informacje

* **Wyzwalacz z wyrażeniem cron** - Wyzwalacz czasomierza dla zadania używa *wyrażenia cron*. Wyrażenie jest ciągiem z pięcioma polami określającymi minutę, godzinę, dzień, miesiąc i dzień tygodnia, aby wyzwolić zadanie. Obsługiwane są częstotliwości do jednej na minutę.

  Na przykład wyrażenie `"0 12 * * Mon-Fri"` wyzwala zadanie w południe UTC w każdy dzień tygodnia. Zobacz [szczegóły](#cron-expressions) w dalszej części tego artykułu.
* **Wiele wyzwalaczy czasomierza** — dodawanie wielu czasomierzy do zadania jest dozwolone, o ile harmonogramy różnią się.
    * Określ wiele wyzwalaczy czasomierza podczas tworzenia zadania lub dodaj je później.
    * Opcjonalnie nazwij wyzwalacze dla łatwiejszego zarządzania lub Zadania usługi ACR zapewni domyślne nazwy wyzwalaczy.
    * Jeśli harmonogramy czasomierza nakładają się na siebie w czasie, zadania usługi ACR wyzwala zadanie w zaplanowanym czasie dla każdego czasomierza.
* **Inne wyzwalacze zadań** — w zadaniu wyzwalanym przez czasomierz można również włączyć wyzwalacze oparte na [zatwierdzaniu kodu źródłowego](container-registry-tutorial-build-task.md) lub [aktualizacjach obrazu podstawowego.](container-registry-tutorial-base-image-update.md) Podobnie jak inne zadania usługi ACR, można również [ręcznie wyzwolić][az-acr-task-run] zaplanowane zadanie.

## <a name="create-a-task-with-a-timer-trigger"></a>Tworzenie zadania za pomocą wyzwalacza czasomierza

Podczas tworzenia zadania za pomocą polecenia [az acr create][az-acr-task-create] można opcjonalnie dodać wyzwalacz czasomierza. Dodaj `--schedule` parametr i przekaż wyrażenie cron dla czasomierza.

Na pierwszy przykład następujące polecenie wyzwala `hello-world` uruchamianie obrazu z usługi Docker Hub codziennie o 21:00 UTC. Zadanie jest uruchamiane bez kontekstu kodu źródłowego.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Uruchom polecenie [az acr,][az-acr-task-show] aby zobaczyć, że wyzwalacz czasomierza jest skonfigurowany. Domyślnie włączony jest również wyzwalacz aktualizacji obrazu podstawowego.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Uruchom zadanie ręcznie za pomocą [zadania az acr,][az-acr-task-run] aby upewnić się, że jest ono poprawnie skonfigurowane:

```azurecli
az acr task run --name mytask --registry myregistry
```

Jeśli kontener działa pomyślnie, dane wyjściowe są podobne do następujących czynności:

```output
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

Po zaplanowanym czasie uruchom polecenie [az acr task list-runs,][az-acr-task-list-runs] aby sprawdzić, czy czasomierz wyzwolił zadanie zgodnie z oczekiwaniami:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Po pomyślnym zakończeniu czasomierza dane wyjściowe są podobne do następujących:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Zarządzanie wyzwalaczami czasomierza

Polecenia [az acr czasomierza zadań][az-acr-task-timer] są używane do zarządzania wyzwalaczami czasomierza dla zadania ACR.

### <a name="add-or-update-a-timer-trigger"></a>Dodawanie lub aktualizowanie wyzwalacza czasomierza

Po utworzeniu zadania opcjonalnie dodaj wyzwalacz czasomierza za pomocą polecenia [az acr task timer add.][az-acr-task-timer-add] W poniższym przykładzie dodano czasomierza czasomierza nazwa *timer2* do *mytask* utworzone wcześniej. Ten zegar wyzwala zadanie codziennie o 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Zaktualizuj harmonogram istniejącego wyzwalacza lub zmień jego stan za pomocą polecenia [az acr timer update.][az-acr-task-timer-update] Na przykład zaktualizuj wyzwalacz o nazwie *timer2,* aby wyzwolić zadanie o 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Wyzwalacze czasomierza listy

Polecenie [az acr timer list][az-acr-task-timer-list] pokazuje wyzwalacze czasomierza skonfigurowane dla zadania:

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

### <a name="remove-a-timer-trigger"></a>Usuwanie wyzwalacza czasomierza

Użyj polecenia [az acr task timer remove,][az-acr-task-timer-remove] aby usunąć wyzwalacz czasomierza z zadania. Poniższy przykład usuwa wyzwalacz *timer2* z *mytask:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Wyrażenia Cron

ACR Tasks używa biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) do interpretacji wyrażeń cron. Obsługiwane wyrażenia w zadaniach usługi ACR mają pięć wymaganych pól oddzielonych białymi znakami:

`{minute} {hour} {day} {month} {day-of-week}`

Strefa czasowa używana z wyrażeniami cron jest skoordynowany czas uniwersalny (UTC). Godziny są w formacie 24-godzinnym.

> [!NOTE]
> Zadania usługi ACR `{second}` nie `{year}` obsługują pola ani pola w wyrażeniach cron. Jeśli kopiujesz wyrażenie cron używane w innym systemie, należy usunąć te pola, jeśli są używane.

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>`"5 * * * *"`</nobr>|co godzinę po 5 minutach po godzinie|
|Wszystkie wartości`*`( )|<nobr>`"* 5 * * *"`</nobr>|co minutę godziny rozpoczynającej się o 5:00 UTC (60 razy dziennie)|
|Zakres (operator)`-`|<nobr>`"0 1-3 * * *"`</nobr>|3 razy dziennie, o 1:00, 2:00 i 3:00 UTC|
|Zestaw wartości (operator)`,`|<nobr>`"20,30,40 * * * *"`</nobr>|3 razy na godzinę, po 20 minutach, 30 minutach i 40 minutach po godzinie|
|Wartość interwału (operator)`/`|<nobr>`"*/10 * * * *"`</nobr>|6 razy na godzinę, po 10 minutach, 20 minutach i tak dalej, po godzinie

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron przykłady

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"*/5 * * * *"`|raz na pięć minut|
|`"0 * * * *"`|raz na górze co godzinę|
|`"0 */2 * * *"`|raz na dwie godziny|
|`"0 9-17 * * *"`|raz na godzinę od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|o 9:30 UTC każdego dnia|
|`"30 9 * * 1-5"`|o 9:30 UTC w każdy dzień powszedni|
|`"30 9 * Jan Mon"`|o 9:30 UTC w każdy poniedziałek stycznia|

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone w tej serii samouczków, w tym rejestry kontenerów lub rejestry, wystąpienie kontenera, magazyn kluczy i jednostkę usługi, wydaj następujące polecenia:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć zadania rejestru kontenerów platformy Azure, które są automatycznie wyzwalane przez czasomierz. 

Na przykład użycie zaplanowanego zadania do czyszczenia repozytoriów w rejestrze można znaleźć w obszarze [Automatyczne czyszczenie obrazów z rejestru kontenerów platformy Azure.](container-registry-auto-purge.md)

Aby zapoznać się z przykładami zadań wyzwalanych przez zatwierdzanie kodu źródłowego lub aktualizacje obrazu podstawowego, zobacz inne artykuły z [serii samouczków Zadania ACR](container-registry-tutorial-quick-task.md).



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
