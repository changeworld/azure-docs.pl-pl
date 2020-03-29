---
title: Uwierzytelnianie krzyżowe rejestru z zadania ACR
description: Konfigurowanie zadania rejestru kontenerów platformy Azure (ACR Task) w celu uzyskania dostępu do innego prywatnego rejestru kontenerów platformy Azure przy użyciu tożsamości zarządzanej dla zasobów platformy Azure
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842501"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Uwierzytelnianie krzyżowe w rejestrze w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure 

W [zadaniu ACR](container-registry-tasks-overview.md)można [włączyć tożsamość zarządzaną dla zasobów platformy Azure](container-registry-tasks-authentication-managed-identity.md). Zadanie można użyć tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez konieczności dostarczania lub zarządzania poświadczeniami. 

W tym artykule dowiesz się, jak włączyć tożsamość zarządzaną w zadaniu, aby wyciągnąć obraz z rejestru innego niż używany do uruchamiania zadania.

Aby utworzyć zasoby platformy Azure, w tym artykule należy uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.68 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

Przykładowe zadanie pobiera obraz podstawowy z innego rejestru kontenerów platformy Azure do tworzenia i wypychania obrazu aplikacji. Aby wyciągnąć obraz podstawowy, należy skonfigurować zadanie z tożsamością zarządzaną i przypisać do niego odpowiednie uprawnienia. 

W tym przykładzie przedstawiono kroki przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu. Wybór tożsamości zależy od potrzeb organizacji.

W rzeczywistym scenariuszu organizacja może obsługiwać zestaw obrazów podstawowych używanych przez wszystkie zespoły programistyczne do tworzenia aplikacji. Te obrazy podstawowe są przechowywane w rejestrze firmowym, a każdy zespół deweloperów ma tylko prawa ściągania. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule potrzebne są dwa rejestry kontenerów platformy Azure:

* Pierwszego rejestru służy do tworzenia i wykonywania zadań usługi ACR. W tym artykule ten rejestr nosi nazwę *myregistry*. 
* Drugi rejestr obsługuje obraz podstawowy używany do tworzenia obrazu. W tym artykule drugi rejestr nosi nazwę *mybaseregistry*. 

Zamień na własne nazwy rejestru w późniejszych krokach.

Jeśli nie masz jeszcze potrzebnych rejestrów kontenerów platformy Azure, zobacz [Szybki start: Tworzenie rejestru kontenerów prywatnych przy użyciu interfejsu wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md) Nie musisz jeszcze wypychywać obrazów do rejestru.

## <a name="prepare-base-registry"></a>Przygotowanie rejestru podstawowego

Najpierw utwórz katalog roboczy, a następnie utwórz plik o nazwie Dockerfile z następującą zawartością. Ten prosty przykład tworzy obraz podstawowy Node.js z obrazu publicznego w centrum platformy Docker.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
W bieżącym katalogu uruchom polecenie [kompilacji az acr,][az-acr-build] aby zbudować i wypchnąć obraz podstawowy do podstawowego rejestru. W praktyce inny zespół lub proces w organizacji może obsługiwać podstawowy rejestr.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definiowanie kroków zadań w pliku YAML

Kroki dla tego [przykładowego zadania wieloetapowego](container-registry-tasks-multi-step.md) są zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md). Utwórz plik `helloworldtask.yaml` o nazwie w lokalnym katalogu roboczym i wklej następującą zawartość. Zaktualizuj wartość w kroku kompilacji `REGISTRY_NAME` o nazwę serwera rejestru podstawowego.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Krok kompilacji używa `Dockerfile-app` pliku w [repozytorium Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) do utworzenia obrazu. Odwołuje `--build-arg` się do rejestru podstawowego, aby wyciągnąć obraz podstawowy. Po pomyślnym skusieniu obraz jest wypychany do rejestru używanego do uruchamiania zadania.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opcja 1: Tworzenie zadania z tożsamością przypisaną przez użytkownika

Kroki opisane w tej sekcji tworzą zadanie i włączą tożsamość przypisaną przez użytkownika. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną do systemu, zobacz [Opcja 2: Tworzenie zadania z tożsamością przypisaną do systemu](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask,* wykonując następujące [polecenie tworzenia zadania az acr.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu roboczym. Parametr `--assign-identity` przekazuje identyfikator zasobu tożsamości przypisanej przez użytkownika. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opcja 2: Tworzenie zadania z tożsamością przypisaną do systemu

Kroki opisane w tej sekcji tworzą zadanie i włączą tożsamość przypisaną do systemu. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez użytkownika, zobacz [Opcja 1: Tworzenie zadania z tożsamością przypisaną przez użytkownika](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask,* wykonując następujące [polecenie tworzenia zadania az acr.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu roboczym. Parametr `--assign-identity` bez wartości włącza tożsamość przypisaną do systemu w zadaniu. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Nadaj uprawnienia do ściągania tożsamości do rejestru podstawowego

W tej sekcji należy podać uprawnienia tożsamości zarządzanej do wyciągnięcia z rejestru *podstawowego, mybaseregistry*.

Użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru podstawowego i przechowywać go w zmiennej:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Polecenie [tworzenia przypisania roli az][az-role-assignment-create] służy `acrpull` do przypisywania tożsamości roli do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Dodawanie poświadczeń rejestru docelowego do zadania

Teraz użyj [polecenia az acr task add,][az-acr-task-credential-add] aby włączyć zadanie do uwierzytelniania w rejestrze podstawowym przy użyciu poświadczeń tożsamości. Uruchom polecenie odpowiadające typowi tożsamości zarządzanej włączonej w zadaniu. Jeśli włączono tożsamość przypisaną przez `--use-identity` użytkownika, przekaż z identyfikatorem klienta tożsamości. Jeśli włączono tożsamość przypisaną systemowi, przekaż `--use-identity [system]`plik .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Aby sprawdzić, czy zadanie, w którym włączono tożsamość zarządzaną, przebiega pomyślnie, ręcznie wykręć zadanie za pomocą polecenia [az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Jeśli zadanie zostanie pomyślnie ukończone, dane wyjściowe są podobne do:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Uruchom polecenie [az acr repozytorium show-tags,][az-acr-repository-show-tags] aby sprawdzić, czy obraz został zbudowany i został pomyślnie wypchnięty do *myregistry:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Przykładowe dane wyjściowe:

```console
cf10
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [włączaniu tożsamości zarządzanej w zadaniu usługi ACR](container-registry-tasks-authentication-managed-identity.md).
* Zobacz [odwołanie do zadania usługi ACR YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
