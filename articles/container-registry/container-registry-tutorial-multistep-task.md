---
title: Samouczek - Wieloetapowe zadanie ACR
description: W tym samouczku dowiesz się, jak skonfigurować zadanie rejestru kontenerów platformy Azure, aby automatycznie wyzwalać wieloetapowy przepływ pracy w celu tworzenia, uruchamiania i wypychania obrazów kontenerów w chmurze podczas zatwierdzania kodu źródłowego do repozytorium Git.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ff32b3095638af6b2b246b99a5dc9219e0020782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402304"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Samouczek: Uruchamianie wieloetapowego przepływu pracy kontenera w chmurze podczas zatwierdzania kodu źródłowego

Oprócz szybkiego [zadania,](container-registry-tutorial-quick-task.md)zadania ACR obsługują wieloetapowe, wielokontenerowe przepływy pracy, które mogą automatycznie wyzwalać podczas zatwierdzania kodu źródłowego do repozytorium Git. 

W tym samouczku dowiesz się, jak użyć przykładowych plików YAML do definiowania zadań wieloetapowych, które skompilowają, uruchamiają i wypychają jeden lub więcej obrazów kontenerów do rejestru podczas zatwierdzania kodu źródłowego. Aby utworzyć zadanie, które automatyzuje tylko pojedynczy obraz na podstawie zatwierdzania kodu, zobacz [Samouczek: Automatyzacja tworzenia obrazu kontenera w chmurze podczas zatwierdzania kodu źródłowego](container-registry-tutorial-build-task.md). Aby zapoznać się z omówieniem zadań ACR, zobacz [Automatyzowanie systemu operacyjnego i poprawek ramowych za pomocą zadań ACR](container-registry-tasks-overview.md),

W tym samouczku:

> [!div class="checklist"]
> * Definiowanie zadania wieloetapowego przy użyciu pliku YAML
> * Tworzenie zadania podrzędnego
> * Opcjonalnie dodaj poświadczenia do zadania, aby włączyć dostęp do innego rejestru
> * Testowanie zadania
> * Wyświetlanie stanu zadania podrzędnego
> * Wyzwalanie zadania po zatwierdzeniu kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](container-registry-tutorial-quick-task.md). Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](container-registry-tutorial-quick-task.md#prerequisites).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli chcesz korzystać z interfejsu wiersza polecenia platformy Azure lokalnie, musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji **2.0.62** lub nowszej i zalogowany przy użyciu [logowania az.][az-login] Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Tworzenie zadania wieloetapowego

Po wykonaniu czynności wymaganych do włączenia zadań usługi ACR do odczytu stanu zatwierdzenia i tworzenia elementów webhook w repozytorium utwórz wieloetapowe zadanie, które wyzwala tworzenie, uruchamianie i wypychanie obrazu kontenera.

### <a name="yaml-file"></a>Plik YAML

Kroki dla zadania wieloetapowego można zdefiniować w [pliku YAML](container-registry-tasks-reference-yaml.md). Pierwsze przykładowe zadanie wieloetapowe dla tego `taskmulti.yaml`samouczka jest zdefiniowane w pliku , który znajduje się w katalogu głównym repozytorium GitHub, które zostało sklonowane:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

To wieloetapowe zadanie wykonuje następujące czynności:

1. Uruchamia `build` krok do tworzenia obrazu z pliku Dockerfile w katalogu roboczym. Obraz jest `Run.Registry`przeznaczony dla rejestru, w którym zadanie jest uruchamiane, i jest oznaczony unikatowym identyfikatorem uruchomienia zadań ACR. 
1. Uruchamia `cmd` krok, aby uruchomić obraz w kontenerze tymczasowym. W tym przykładzie rozpoczyna długotrwały kontener w tle i zwraca identyfikator kontenera, a następnie zatrzymuje kontener. W scenariuszu rzeczywistym może zawierać kroki, aby przetestować uruchomiony kontener, aby upewnić się, że działa poprawnie.
1. W `push` kroku wypycha obraz, który został zbudowany do rejestru uruchamiania.

### <a name="task-command"></a>Zadanie, polecenie

Najpierw wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne środowiskowe nie zostaną zapełniane, należy ręcznie zastąpić każdą wartość wszędzie tam, gdzie pojawia się ona w przykładowych poleceniach.

[![Uruchom osadź](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teraz utwórz zadanie, wykonując następujące [polecenie tworzenia zadania az acr:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

To zadanie określa, że każdy kod *master* czasu jest zaakrążany `--context`do gałęzi głównej w repozytorium określonym przez , Zadania ACR uruchomi zadanie wieloetapowe z kodu w tej gałęzi. Plik YAML określony `--file` przez z katalogu głównego repozytorium definiuje kroki. 

Dane wyjściowe z pomyślnie wykonanego polecenia [az acr task create][az-acr-task-create] przypominają następujące dane:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testowanie wieloetapowego przepływu pracy

Aby przetestować zadanie wieloetapowe, uruchom je ręcznie, wykonując polecenie [az acr:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Domyślnie polecenie `az acr task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. Dane wyjściowe pokazuje postęp uruchamiania każdego z kroków zadania. Poniższe dane wyjściowe są skondensowane, aby wyświetlić kluczowe kroki.

```output
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Wyzwalanie kompilacji za pomocą zatwierdzenia

Teraz, po przetestowaniu zadania przez jego ręczne uruchomienie, wyzwól je automatycznie za pomocą zmiany kodu źródłowego.

Najpierw upewnij się, że jesteś w katalogu zawierającym lokalny klon [repozytorium][sample-repo]:

```console
cd acr-build-helloworld-node
```

Następnie uruchom następujące polecenia do utworzenia, zatwierdzenia i wypchnięcia nowego pliku do rozwidlenia repozytorium w usłudze GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Być może podczas wykonywania polecenia `git push` będzie konieczne wprowadzenie poświadczeń usługi GitHub. Podaj nazwę użytkownika usługi GitHub i wprowadź osobisty token dostępu (PAT) utworzony wcześniej dla hasła.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po wypchnięciu zatwierdzenia do repozytorium element webhook utworzony przez zadania usługi ACR uruchamia i uruchamia zadanie w rejestrze kontenerów platformy Azure. Wyświetl dzienniki dla aktualnie uruchomionego zadania, aby sprawdzić i monitorować postęp kompilacji:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Dane wyjściowe są podobne do następujących danych i przedstawiają aktualnie (lub ostatnio) wykonywane zadanie:

```output
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Lista kompilacji

Aby wyświetlić listę przebiegów zadań, które usługa ACR Tasks wykonała dla rejestru, uruchom polecenie [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Dane wyjściowe polecenia powinny wyglądać podobnie do następujących danych. Wyświetlone zostaną przebiegi wykonane przez usługę ACR Tasks, a dla najnowszego zadania w kolumnie TRIGGER pojawi się pozycja „Git Commit”:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Tworzenie wieloetapowego zadania wieloetapowego z wieloma rejestrami

Zadania usługi ACR domyślnie mają uprawnienia do wypychania lub ściągania obrazów z rejestru, w którym jest uruchamiane zadanie. Można uruchomić zadanie wieloetapowe, które jest przeznaczone dla jednego lub więcej rejestrów oprócz rejestru uruchamiania. Na przykład może być konieczne tworzenie obrazów w jednym rejestrze i przechowywanie obrazów z różnymi znacznikami w drugim rejestrze, do którego dostęp uzyskuje system produkcyjny. W tym przykładzie pokazano, jak utworzyć takie zadanie i podać poświadczenia dla innego rejestru.

Jeśli nie masz jeszcze drugiego rejestru, utwórz go w tym przykładzie. Jeśli potrzebujesz rejestru, zobacz [poprzedni samouczek](container-registry-tutorial-quick-task.md) lub [Szybki start: tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

Aby utworzyć zadanie, potrzebna jest nazwa serwera logowania rejestru, który jest *mycontainerregistrydate.azurecr.io* formularza (wszystkie małe litery). W tym przykładzie należy użyć drugiego rejestru do przechowywania obrazów oznaczonych według daty kompilacji.

### <a name="yaml-file"></a>Plik YAML

Drugie przykładowe zadanie wieloetapowe dla tego `taskmulti-multiregistry.yaml`samouczka jest zdefiniowane w pliku , który znajduje się w katalogu głównym repozytorium GitHub, które zostało sklonowane:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

To wieloetapowe zadanie wykonuje następujące czynności:

1. Uruchamia `build` dwa kroki do tworzenia obrazów z pliku Dockerfile w katalogu roboczym:
    * Pierwszy jest `Run.Registry`przeznaczony dla rejestru, w którym zadanie jest uruchamiane, i jest oznaczony identyfikatorem uruchamiania zadań ACR. 
    * Drugi jest przeznaczony dla rejestru identyfikowanego przez wartość `regDate`, która jest ustawiana `values.yaml` podczas `az acr task create`tworzenia zadania (lub dostarczania za pośrednictwem pliku zewnętrznego przekazanego do ). Ten obraz jest oznaczony datą uruchomienia.
1. Uruchamia `cmd` krok, aby uruchomić jeden z kontenerów zbudowany. W tym przykładzie rozpoczyna długotrwały kontener w tle i zwraca identyfikator kontenera, a następnie zatrzymuje kontener. W scenariuszu rzeczywistym można przetestować uruchomiony kontener, aby upewnić się, że działa poprawnie.
1. W `push` kroku wypycha obrazy, które zostały zbudowane, pierwszy do rejestru uruchamiania, `regDate`drugi do rejestru zidentyfikowanego przez .

### <a name="task-command"></a>Zadanie, polecenie

Korzystając ze zdefiniowanych wcześniej zmiennych środowiskowych powłoki, utwórz zadanie, wykonując następujące [polecenie tworzenia zadania az acr.][az-acr-task-create] Zastąp nazwę rejestru dla *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Dodawanie poświadczeń zadania

Aby wypchnąć obrazy do rejestru `regDate`identyfikowanego przez wartość , użyj [polecenia az acr task add,][az-acr-task-credential-add] aby dodać poświadczenia logowania dla tego rejestru do zadania.

W tym przykładzie zaleca się utworzenie [jednostki usługi](container-registry-auth-service-principal.md) z dostępem do rejestru o zakresie do roli *AcrPush.* Aby utworzyć jednostkę usługi, zobacz ten [skrypt interfejsu wiersza polecenia platformy Azure](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Przekaż identyfikator aplikacji głównej usługi i `az acr task credential add` hasło w następującym poleceniu:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Interfejs wiersza polecenia zwraca nazwę dodanego serwera logowania rejestru.

### <a name="test-the-multi-step-workflow"></a>Testowanie wieloetapowego przepływu pracy

Podobnie jak w poprzednim przykładzie, aby przetestować zadanie wieloetapowe, wyzwolić je ręcznie, wykonując polecenie [az acr.][az-acr-task-run] Aby wyzwolić zadanie z zatwierdzeniem do repozytorium Git, zobacz [sekcję Wyzwalanie kompilacji z zatwierdzeniem](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Domyślnie polecenie `az acr task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. Jak poprzednio, dane wyjściowe pokazuje postęp uruchamiania każdego z kroków zadania. Dane wyjściowe są skondensowane, aby wyświetlić kluczowe kroki.

Dane wyjściowe:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak tworzyć wieloetapowe zadania oparte na wielu kontenerach, które automatycznie wyzwalają się podczas zatwierdzania kodu źródłowego do repozytorium Git. Aby uzyskać zaawansowane funkcje zadań wieloetapowych, w tym równoległe i zależne wykonanie kroku, zobacz [odwołanie DO ZADAŃ ACR YAML](container-registry-tasks-reference-yaml.md). Przejdź do kolejnego samouczka, aby dowiedzieć się, jak utworzyć zadania, które wyzwalają kompilacje przy aktualizacji obrazu podstawowego obrazu kontenera.

> [!div class="nextstepaction"]
> [Automatyczne kompilacje przy aktualizacji obrazu podstawowego](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
