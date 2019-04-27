---
title: (PRZESTARZAŁE) Przewodnik Szybki Start — klaster Azure Docker CE dla systemu Linux
description: Szybka nauka tworzenia klastra Docker CE dla kontenerów systemu Linux w usłudze Azure Container Service za pomocą interfejsu wiersza polecenia platformy Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: a7a7455ce9167a9c480d317d50fdce49e2ef06a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721801"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(PRZESTARZAŁE) Wdrażanie klastra Docker CE

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym przewodniku Szybki start klaster Docker CE jest wdrażany za pomocą interfejsu wiersza polecenia platformy Azure. Następnie w klastrze jest wdrażana i uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis. Po ukończeniu aplikacja będzie dostępna w Internecie.

Tryb Docker CE w usłudze Azure Container Service jest w wersji zapoznawczej i **nie powinien być używany w przypadku obciążeń produkcyjnych**.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *westus2* lokalizacji.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Dane wyjściowe:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Tworzenie klastra Docker Swarm

Utwórz klaster Docker CE w usłudze Azure Container Service za pomocą polecenia [az acs create](/cli/azure/acs#az-acs-create). Aby uzyskać informacje na region availaiblity Docker CE, zobacz [regionów usługi ACS dla Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)

W poniższym przykładzie tworzony jest klaster o nazwie *mySwarmCluster* z jednym węzłem głównym systemu Linux i trzema węzłami agenta systemu Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

W niektórych przypadkach, np. ograniczonej wersji próbnej, subskrypcja platformy Azure ma ograniczony dostęp do zasobów platformy Azure. Jeśli wdrożenie nie powiedzie się z powodu ograniczonej liczby dostępnych rdzeni, zmniejsz domyślną liczbę agentów, dodając `--agent-count 1` do polecenia [az acs create](/cli/azure/acs#az-acs-create). 

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

W tym przewodniku Szybki start potrzebne będą nazwy FQDN zarówno węzła głównego Docker Swarm, jak i puli agenta Docker. Uruchom następujące polecenie, aby zwrócić nazwy FQDN węzła głównego i agenta.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Dane wyjściowe:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Utwórz tunel SSH do węzła głównego Swarm. Zamień `MasterFQDN` na adres FQDN węzła głównego Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Ustaw zmienną środowiskową `DOCKER_HOST`. Umożliwia to uruchamianie poleceń Docker względem klastra Docker Swarm bez konieczności określania nazwy hosta.

```bash
export DOCKER_HOST=localhost:2374
```

Teraz można przystąpić do uruchamiania usług Docker w klastrze Docker Swarm.


## <a name="run-the-application"></a>Uruchamianie aplikacji

Utwórz plik o nazwie `azure-vote.yaml` i skopiuj do niego poniższą zawartość.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Uruchom polecenie [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/), aby utworzyć usługę Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Dane wyjściowe:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Za pomocą polecenia [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) zwróć stan wdrożenia aplikacji.

```bash
docker stack ps azure-vote
```

Gdy pozycje `CURRENT STATE` poszczególnych usług będą miały wartość `Running`, aplikacja będzie gotowa.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testowanie aplikacji

Przejdź do nazwy FQDN puli agenta Swarm w celu przetestowania aplikacji Azure Vote.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Usuwanie klastra
Gdy klaster nie będzie już potrzebny, możesz usunąć grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku Szybki start wcześniej utworzone obrazy kontenera zostały użyte w celu utworzenia usługi Docker. Powiązany kod aplikacji, plik Dockerfile i plik Compose są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wdrożono klaster Docker Swarm oraz wdrożono w nim aplikację obsługującą wiele kontenerów.

Aby dowiedzieć się więcej na temat integracji klastra Docker swarm przy użyciu infrastruktury DevOps platformy Azure, nadal CI/CD przy użyciu rozwiązań Docker Swarm i DevOps platformy Azure.

> [!div class="nextstepaction"]
> [Ciągła Integracja/ciągłe dostarczanie z usługami Docker Swarm i DevOps platformy Azure](./container-service-docker-swarm-setup-ci-cd.md)
