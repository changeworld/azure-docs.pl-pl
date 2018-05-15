---
title: Samouczek — automatyzowanie kompilacji obrazu kontenera za pomocą usługi Azure Container Registry Build
description: Z tego samouczka dowiesz się, jak skonfigurować zadanie kompilacji w celu automatycznego wyzwalania kompilacji obrazu kontenera w chmurze, gdy zatwierdzasz kod źródłowy do repozytorium Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fba499441d092f4dce09d13d607dfc5de65d98b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Samouczek: automatyzowanie kompilacji obrazu kontenera za pomocą usługi Azure Container Registry Build

Oprócz [szybkiej kompilacji](container-registry-tutorial-quick-build.md) usługa ACR Build obsługuje automatyczne kompilacje obrazu kontenera platformy Docker za pomocą *zadań kompilacji*. W tym samouczku używany jest interfejs wiersza polecenia platformy Azure do utworzenia zadania kompilacji, które automatycznie wyzwoli kompilacje obrazu w chmurze po zatwierdzeniu kodu źródłowego do repozytorium Git.

Z tego samouczka, który jest drugą częścią serii, nauczysz się wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie zadania kompilacji
> * Testowanie zadania kompilacji
> * Wyświetlanie stanu kompilacji
> * Wyzwalanie zadania kompilacji po zatwierdzeniu kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](container-registry-tutorial-quick-build.md). Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](container-registry-tutorial-quick-build.md#prerequisites).

> [!IMPORTANT]
> Usługa ACR Build jest obecnie dostępna w wersji zapoznawczej i jest obsługiwana wyłącznie przez rejestry kontenerów platformy Azure w regionach **Wschodnie stany USA** (eastus) i **Europa Zachodnia** (westeurope). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure lokalnie, musisz zainstalować wersję **2.0.32** interfejsu wiersza polecenia platformy Azure lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0][azure-cli].

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="get-sample-code"></a>Pobieranie przykładowego kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](container-registry-tutorial-quick-build.md) oraz że przykładowe repozytorium zostało rozwidlone i sklonowane. Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](container-registry-tutorial-quick-build.md#prerequisites).

### <a name="container-registry"></a>Rejestr kontenerów

Aby ukończyć ten samouczek, w Twojej subskrypcji platformy Azure musisz posiadać rejestr kontenerów platformy Azure. Jeśli potrzebujesz rejestru, zobacz [poprzedni samouczek](container-registry-tutorial-quick-build.md) lub [Szybki start: tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Zadanie kompilacji

Zadanie kompilacji definiuje właściwości automatycznej kompilacji, w tym lokalizację kodu źródłowego obrazu kontenera i zdarzenie, które wyzwala kompilację. Gdy wystąpi zdarzenie zdefiniowane w zadaniu kompilacji, na przykład zatwierdzenie do repozytorium Git, usługa ACR Build inicjuje kompilację obrazu kontenera w chmurze. Domyślnie wypycha następnie pomyślnie skompilowany obraz do rejestru kontenera platformy Azure określonego w zadaniu.

Usługa ACR Build obsługuje obecnie następujące wyzwalacze zadań kompilacji:

* Zatwierdzenie do repozytorium Git
* Aktualizacja obrazów podstawowych

## <a name="create-a-build-task"></a>Tworzenie zadania kompilacji

W tej sekcji należy najpierw utworzyć osobisty token dostępu GitHub (PAT, personal access token) do użycia z usługą ACR Build. Następnie należy utworzyć zadanie kompilacji, które wyzwala kompilację po zatwierdzeniu kodu do rozwidlenia repozytorium.

### <a name="create-a-github-personal-access-token"></a>Tworzenie osobistego tokenu dostępu GitHub

Aby wyzwolić kompilację po zatwierdzeniu do repozytorium Git, usługa ACR Build potrzebuje osobistego tokenu dostępu (PAT) w celu uzyskania dostępu do repozytorium. Wykonaj następujące kroki, aby wygenerować token PAT w witrynie GitHub:

1. Przejdź do strony tworzenia tokenu PAT w witrynie GitHub pod adresem https://github.com/settings/tokens/new
1. Wprowadź krótki **opis** dla tokenu, na przykład „Przykładowe zadanie ACR Build”
1. W obszarze **repo** zaznacz pozycje **repo:status** i **public_repo**

   ![Zrzut ekranu strony generowania osobistego tokenu dostępu w usłudze GitHub][build-task-01-new-token]

1. Wybierz przycisk **Generate token** (Generuj token) (może zostać wyświetlony monit o potwierdzenie hasła)
1. Skopiuj i zapisz wygenerowany token w **bezpiecznej lokalizacji** (użyjesz tego tokenu podczas definiowania zadania kompilacji w następnej sekcji)

   ![Zrzut ekranu przedstawiający wygenerowany osobisty token dostępu w usłudze GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Tworzenie zadania kompilacji

Po ukończeniu kroków wymaganych do włączenia usługi ACR Build w celu odczytywania stanu zatwierdzenia i tworzenia elementów webhook w repozytorium możesz utworzyć zadanie kompilacji, które wyzwala kompilację obrazu kontenera po zatwierdzeniu do repozytorium.

Najpierw wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Nie jest to ściśle wymagane, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne nie zostaną wypełnione, należy ręcznie zastąpić każdą wartość we wszystkich miejscach występowania w przykładowych poleceniach.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Teraz utwórz zadanie kompilacji, wykonując następujące polecenie [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

To zadanie kompilacji określa, że za każdym razem, gdy kod jest zatwierdzany do *głównej* gałęzi w repozytorium określonym przez element `--context`, usługa ACR Build skompiluje obraz kontenera z kodu w tej gałęzi. Argument `--image` określa wartość sparametryzowaną elementu `{{.Build.ID}}` dla części wersji tagu obrazu, zapewniając, że skompilowany obraz jest powiązany z określoną kompilacją i jest jednoznacznie oznakowany.

Dane wyjściowe z pomyślnie wykonanego polecenia [az acr build-task create][az-acr-build-task-create] przypominają następujące dane:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-04-18T23:14:45.905395+00:00",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "Github"
  },
  "status": "enabled",
  "tags": null,
  "timeout": null,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}

```

## <a name="test-the-build-task"></a>Testowanie zadania kompilacji

Masz teraz zadanie kompilacji, które definiuje kompilację. Aby przetestować definicję kompilacji, należy ręcznie wyzwolić kompilację, wykonując polecenie [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Domyślnie polecenie `az acr build-task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. W tym miejscu dane wyjściowe wskazują, że kompilacja **eastus2** została umieszczona w kolejce i skompilowana.

```console
$ az acr build-task run --registry mycontainerregistry --name buildhelloworld
Queued a build with build-id: eastus2.
Starting to stream the logs...
Cloning into '/root/acr-builder/src'...
time="2018-04-19T00:06:20Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
ffef1347389a008c9a8bfdf8c6a0ed78b0479894
time="2018-04-19T00:06:20Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-04-19T00:06:20Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:eastus2 ."
Sending build context to Docker daemon  182.8kB
Step 1/5 : FROM node:9-alpine
9: Pulling from library/node
Digest: sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90
Status: Image is up to date for node:9-alpine
 ---> aa3e171e4e95
Step 2/5 : COPY . /src
 ---> e1c04dc2993b

[...]

6e5e20cbf4a7: Layer already exists
b69680cb4898: Pushed
b54af9b858b7: Pushed
eastus2: digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6 size: 2423
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloworld:eastus2"
"["mycontainerregistry.azurecr.io/helloworld@sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6"]"
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloworld
    tag: eastus2
    digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 6944c6bd0602f96e5fecf56ff8d66e2d268223e3

Build complete
Build ID: eastus2 was successful after 39.789138274s
```

## <a name="view-build-status"></a>Wyświetlanie stanu kompilacji

Warto co pewien czas wyświetlić stan trwającej kompilacji, która nie została wyzwolona ręcznie. Na przykład podczas rozwiązywania problemów z kompilacjami wyzwalanymi za pomocą zatwierdzeń kodu źródłowego. W tej sekcji kompilacja zostanie wyzwolona ręcznie, ale domyślne zachowanie przesyłania strumieniowego dziennika kompilacji do konsoli zostanie pominięte. Następnie należy użyć polecenia `az acr build-task logs` do monitorowania bieżącej kompilacji.

Po pierwsze wyzwól kompilację ręcznie jak poprzednio, ale określ argument `--no-logs`, by pominąć rejestrowanie do konsoli:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Następnie użyj polecenia `az build-task logs`, aby wyświetlić dziennik aktualnie uruchomionych kompilacji:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Dziennik dla aktualnie uruchomionych kompilacji jest przesyłany strumieniowo do konsoli i powinien wyglądać podobnie do następujących danych wyjściowych (tu pokazane w wersji obciętej):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus3

[...]

Build complete
Build ID: eastus3 was successful after 30.076988169s
```

## <a name="trigger-a-build-with-a-commit"></a>Wyzwalanie kompilacji za pomocą zatwierdzenia

Teraz, po przetestowaniu zadania kompilacji przez ręczne uruchomienie go, wyzwól je automatycznie za pomocą zmiany kodu źródłowego.

Najpierw upewnij się, że jesteś w katalogu zawierającym lokalny klon [repozytorium][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Następnie uruchom następujące polecenia do utworzenia, zatwierdzenia i wypchnięcia nowego pliku do rozwidlenia repozytorium w usłudze GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Być może podczas wykonywania polecenia `git push` będzie konieczne wprowadzenie poświadczeń usługi GitHub. Podaj nazwę użytkownika usługi GitHub i wprowadź osobisty token dostępu (PAT) utworzony wcześniej dla hasła.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po wypchnięciu zatwierdzenia do repozytorium element webhook utworzony przez usługę ACR Build wyzwala i rozpoczyna kompilację w usłudze Azure Container Registry. Wyświetl dzienniki kompilacji dla aktualnie uruchomionych kompilacji, aby sprawdzić i monitorować postęp kompilacji:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Dane wyjściowe są podobne do następujących danych i przedstawiają aktualnie (lub ostatnio) wykonywaną kompilację:

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus4

[...]

Build complete
Build ID: eastus4 was successful after 28.9587031s
```

## <a name="list-builds"></a>Lista kompilacji

Aby wyświetlić listę kompilacji, które usługa ACR Build wykonała dla rejestru, uruchom polecenie [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Dane wyjściowe polecenia powinny wyglądać podobnie do następujących danych. Wyświetlone zostają kompilacje wykonane przez usługę ACR Build, a dla najnowszej kompilacji w kolumnie TRIGGER pojawia się pozycja „Git Commit”:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
eastus4     buildhelloworld  Linux       Succeeded  Git Commit  2018-04-20T22:50:27Z  00:00:35
eastus3     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:47:19Z  00:00:30
eastus2     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:46:14Z  00:00:55
eastus1                                  Succeeded  Manual      2018-04-20T22:38:22Z  00:00:55
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak używać zadania kompilacji w celu automatycznego wyzwalania kompilacji obrazu kontenera na platformie Azure po zatwierdzeniu kodu źródłowego do repozytorium Git. Przejdź do kolejnego samouczka, aby dowiedzieć się, jak utworzyć zadania kompilacji, które wyzwalają kompilacje przy aktualizacji obrazu podstawowego obrazu kontenera.

> [!div class="nextstepaction"]
> [Automatyczne kompilacje przy aktualizacji obrazu podstawowego](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
