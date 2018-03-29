---
title: Szybki start — klaster Azure Docker Swarm dla systemu Linux
description: Szybka nauka tworzenia klastra Docker Swarm dla kontenerów systemu Linux w usłudze Azure Container Service za pomocą interfejsu wiersza polecenia platformy Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7bd781d2fd71c6075cb458b222a93953a22892b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-docker-swarm-cluster"></a>Wdrażanie klastra Docker Swarm

W tym przewodniku Szybki start klaster Docker Swarm jest wdrażany za pomocą interfejsu wiersza polecenia platformy Azure. Następnie w klastrze jest wdrażana i uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis. Po ukończeniu aplikacja będzie dostępna w Internecie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Dane wyjściowe:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Tworzenie klastra Docker Swarm

Utwórz klaster Docker Swarm w usłudze Azure Container Service za pomocą polecenia [az acs create](/cli/azure/acs#az_acs_create). 

W poniższym przykładzie tworzony jest klaster o nazwie *mySwarmCluster* z jednym węzłem głównym systemu Linux i trzema węzłami agenta systemu Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

W niektórych przypadkach, np. ograniczonej wersji próbnej, subskrypcja platformy Azure ma ograniczony dostęp do zasobów platformy Azure. Jeśli wdrożenie nie powiedzie się z powodu ograniczonej liczby dostępnych rdzeni, zmniejsz domyślną liczbę agentów, dodając `--agent-count 1` do polecenia [az acs create](/cli/azure/acs#az_acs_create). 

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze sformatowanym przy użyciu formatu JSON.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

W tym przewodniku Szybki start potrzebne będą adresy IP zarówno węzła głównego Docker Swarm, jak i puli agenta Docker. Uruchom następujące polecenie, aby zwrócić oba adresy IP.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Dane wyjściowe:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Utwórz tunel SSH do węzła głównego Swarm. Zamień `IPAddress` na adres IP węzła głównego Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Ustaw zmienną środowiskową `DOCKER_HOST`. Umożliwia to uruchamianie poleceń Docker względem klastra Docker Swarm bez konieczności określania nazwy hosta.

```bash
export DOCKER_HOST=:2375
```

Teraz można przystąpić do uruchamiania usług Docker w klastrze Docker Swarm.


## <a name="run-the-application"></a>Uruchamianie aplikacji

Utwórz plik o nazwie `docker-compose.yaml` i skopiuj do niego poniższą zawartość.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Uruchom następujące polecenie, aby utworzyć usługę Azure Vote.

```bash
docker-compose up -d
```

Dane wyjściowe:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Testowanie aplikacji

Przejdź do adresu IP puli agenta Swarm w celu przetestowania aplikacji Azure Vote.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Usuwanie klastra
Gdy klaster nie będzie już potrzebny, możesz usunąć grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku Szybki start wcześniej utworzone obrazy kontenera zostały użyte w celu utworzenia usługi Docker. Powiązany kod aplikacji, plik Dockerfile i plik Compose są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Docker Swarm oraz wdrożono w nim aplikację obsługującą wiele kontenerów.

Aby dowiedzieć się więcej na temat integracji klastra Docker Swarm z programem Visual Studio Team Services, przejdź do części „Ciągła integracja/ciągłe dostarczanie z usługami Swarm i VSTS”.

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe dostarczanie z usługami Swarm i VSTS](./container-service-docker-swarm-setup-ci-cd.md)
