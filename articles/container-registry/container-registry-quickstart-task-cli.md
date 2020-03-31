---
title: Szybki start — tworzenie & uruchamianie obrazu kontenera
description: Szybko uruchamiaj zadania za pomocą usługi Azure Container Registry w celu tworzenia i uruchamiania obrazu kontenera platformy Docker na żądanie w chmurze.
ms.topic: quickstart
ms.date: 01/31/2020
ms.openlocfilehash: f08f10dd170acaa8594ad5a47f5ef58e27288b10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76986278"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Szybki start: tworzenie i uruchamianie obrazu kontenera przy użyciu zadań rejestru kontenerów platformy Azure

W tym przewodniku Szybki start używasz poleceń zadań rejestru kontenerów platformy Azure, aby szybko tworzyć, wypychać i uruchamiać obraz kontenera platformy Docker natywnie na platformie Azure, pokazując, jak odciążyć cykl programowania "inner-loop" do chmury. [Zadania usługi ACR][container-registry-tasks-overview] to zestaw funkcji w rejestrze kontenerów platformy Azure, które ułatwiają zarządzanie obrazami kontenerów i modyfikowanie ich w całym cyklu życia kontenera. 

Po tym przewodniku Szybki start zapoznaj się z bardziej zaawansowanymi funkcjami zadań ACR. Zadania usługi ACR można zautomatyzować kompilacje obrazów na podstawie zatwierdzania kodu lub aktualizacji obrazu podstawowego lub przetestować wiele kontenerów, równolegle, wśród innych scenariuszy. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][azure-account] przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas pracy z tym przewodnikiem Szybki start możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, zaleca się wersję 2.0.58 lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli nie masz jeszcze rejestru kontenerów, najpierw utwórz grupę zasobów za pomocą polecenia [utwórz grupę AZ.][az-group-create] Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Utwórz rejestr kontenerów za pomocą polecenia [az acr create.][az-acr-create] Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie jest używany *myContainerRegistry008.* Zaktualizuj ją do unikatowej wartości.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

W tym przykładzie tworzy *rejestr podstawowy,* opcja zoptymalizowana pod kątem kosztów dla deweloperów dowiedzieć się o usłudze Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług, zobacz [Jednostki SKU rejestru kontenerów][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Tworzenie obrazu z pliku Dockerfile

Teraz użyj usługi Azure Container Registry do utworzenia obrazu. Najpierw utwórz katalog roboczy, a następnie utwórz plik dockerfile o nazwie *Dockerfile* z następującą zawartością. Jest to prosty przykład do tworzenia obrazu kontenera systemu Linux, ale można utworzyć własny standardowy Dockerfile i tworzyć obrazy dla innych platform. Przykłady poleceń w tym artykule są sformatowane dla powłoki bash.

```bash
echo FROM hello-world > Dockerfile
```

Uruchom [polecenie kompilacji az acr,][az-acr-build] aby zbudować obraz. Po pomyślnym skusieniu obraz jest wypychany do rejestru. Poniższy przykład wypycha `sample/hello-world:v1` obraz. `.` Na końcu polecenia ustawia lokalizację Dockerfile, w tym przypadku bieżącego katalogu.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

Dane wyjściowe z pomyślnej kompilacji i wypychania jest podobny do następujących:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Uruchamianie obrazu

Teraz szybko uruchom obraz, który został utworzony i wypchnięty do rejestru. W tym miejscu można użyć [az acr run,][az-acr-run] aby uruchomić polecenie kontenera. W przepływie pracy tworzenia kontenerów może to być krok sprawdzania poprawności przed wdrożeniem obrazu lub można dołączyć to polecenie do [wieloetapowego pliku YAML][container-registry-tasks-multi-step]. 

Poniższy przykład `$Registry` służy do określenia rejestru, w którym uruchomisz polecenie:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Parametr `cmd` w tym przykładzie uruchamia kontener w `cmd` konfiguracji `docker run` domyślnej, `docker` ale obsługuje dodatkowe parametry lub nawet inne polecenia.

Dane wyjściowe będą podobne do następujących:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, rejestr kontenerów i przechowywane w nim obrazy kontenerów nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto funkcji zadań usługi ACR do szybkiego tworzenia, wypychania i uruchamiania obrazu kontenera platformy Docker natywnie na platformie Azure, bez lokalnej instalacji platformy Docker. Przejdź do samouczków zadań rejestru kontenerów platformy Azure, aby dowiedzieć się więcej o używaniu zadań usługi ACR do automatyzacji kompilacji i aktualizacji obrazów.

> [!div class="nextstepaction"]
> [Samouczki zadań rejestru kontenerów platformy Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
