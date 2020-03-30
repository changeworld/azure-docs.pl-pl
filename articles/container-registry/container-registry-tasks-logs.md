---
title: Wyświetlanie dzienników uruchamiania zadań — zadania
description: Jak wyświetlać dzienniki uruchamiania generowane przez zadania usługi ACR i zarządzać nimi.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246970"
---
# <a name="view-and-manage-task-run-logs"></a>Wyświetlanie dzienników przebiegu zadań i zarządzanie nimi

Każde zadanie uruchomione w [zadaniach rejestru kontenerów platformy Azure](container-registry-tasks-overview.md) generuje dane wyjściowe dziennika, które można sprawdzić, aby ustalić, czy kroki zadania zostały pomyślnie uruchomione. 

W tym artykule wyjaśniono, jak wyświetlać dzienniki uruchamiania zadań i zarządzać nimi.

## <a name="view-streamed-logs"></a>Wyświetlanie dzienników przesyłanych strumieniowo

Podczas ręcznego wyzwalania zadania dane wyjściowe dziennika są przesyłane strumieniowo bezpośrednio do konsoli. Na przykład, gdy wyzwolisz zadanie ręcznie za pomocą [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run)lub [az acr polecenia uruchamiania zadania,](/cli/azure/acr/task#az-acr-task-run) zobaczysz wyjście dziennika przesyłane strumieniowo do konsoli. 

Następujące przykładowe polecenie [az acr run](/cli/azure/acr#az-acr-run) ręcznie wyzwala zadanie, które uruchamia kontener pobrany z tego samego rejestru:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Dziennik przesyłany strumieniowo:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Wyświetlanie przechowywanych dzienników 

Magazyny rejestru kontenerów platformy Azure uruchamiają dzienniki dla wszystkich zadań. Dzienniki przebiegu przechowywanego można wyświetlić w witrynie Azure portal. Możesz też użyć polecenia [az acr task logs,](/cli/azure/acr/task#az-acr-task-logs) aby wyświetlić wybrany dziennik. Domyślnie dzienniki są przechowywane przez 30 dni.

Jeśli zadanie jest automatycznie wyzwalane, na przykład przez aktualizację kodu źródłowego, dostęp do przechowywanych dzienników jest *jedynym* sposobem wyświetlania dzienników uruchamiania. Automatyczne wyzwalacze zadań obejmują zatwierdzanie kodu źródłowego lub żądania ściągnięcia, aktualizacje obrazu podstawowego i wyzwalacze czasomierza.

Aby wyświetlić dzienniki uruchamiania w portalu:

1. Przejdź do rejestru kontenerów.
1. W **obszarze Usługi**wybierz pozycję **Zadania** > **są uruchamiane**.
1. Wybierz **identyfikator uruchomienia,** aby wyświetlić stan uruchomienia i uruchomić dzienniki. Dziennik zawiera te same informacje co dziennik przesyłany strumieniowo, jeśli jest generowany.

![Wyświetlanie portalu logowania z uruchomieniem zadania](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Aby wyświetlić dziennik przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom [dzienniki zadań az acr](/cli/azure/acr/task#az-acr-task-logs) i określ identyfikator uruchomienia, nazwę zadania lub określony obraz utworzony przez zadanie kompilacji. Jeśli określono nazwę zadania, polecenie pokazuje dziennik ostatniego utworzonego uruchomienia.

W poniższym przykładzie wyprowadza dziennik do uruchomienia z identyfikatorem *cf4:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatywne przechowywanie dzienników

Można zapisać dzienniki uruchamiania zadań w lokalnym systemie plików lub użyć alternatywnego rozwiązania do archiwizacji, takiego jak Usługa Azure Storage.

Na przykład utwórz lokalny katalog *dzienników zadań* i przekieruj dane [wyjściowe dzienników zadań az acr](/cli/azure/acr/task#az-acr-task-logs) do pliku lokalnego:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Można również zapisać lokalne pliki dziennika w usłudze Azure Storage. Na przykład użyj [interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md), [witryny Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md)lub innych metod przekazywania plików do konta magazynu.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zadaniach rejestru kontenerów platformy Azure](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
