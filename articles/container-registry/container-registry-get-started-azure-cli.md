---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure — interfejs wiersza polecenia platformy Azure
description: Szybka nauka tworzenia rejestru prywatnego platformy Docker przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 6c511c56ab8df14cc6ea81363772ae0fd6d61272
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309531"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Szybki start: tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Container Registry to zarządzana usługa rejestru kontenerów platformy Docker używana do przechowywania prywatnych obrazów kontenerów Docker. W tym przewodniku znajdują się szczegółowe informacje dotyczące tworzenia wystąpienia usługi Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Musisz mieć również zainstalowane lokalnie środowisko Docker. Platforma Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows]lub [Linux][docker-linux] .

Ze względu na to, że usługa Azure Cloud Shell nie zawiera wszystkich wymaganych składników platformy Docker (demon `dockerd`), nie można używać usługi Cloud Shell na potrzeby tego przewodnika Szybki start.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje o dostępnych warstwach usług, zobacz [jednostki SKU rejestru kontenerów][container-registry-skus].

Utwórz wystąpienie usługi ACR za pomocą polecenia [az acr create][az-acr-create]. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie użyto nazwy *myContainerRegistry007*. Zaktualizuj ją do unikatowej wartości.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po utworzeniu rejestru dane wyjściowe będą podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Zanotuj wartość `loginServer` w danych wyjściowych, która jest w pełni kwalifikowaną nazwą rejestru (wszystkie małe litery). W dalszej części tego przewodnika Szybki start wartość `<acrName>` jest używana jako symbol zastępczy nazwy rejestru kontenerów.

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do rejestru. Aby to zrobić, użyj polecenia [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Po ukończeniu polecenie zwraca komunikat `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Poniższy przykład wyświetla listę repozytoriów w Twoim rejestrze:

```azurecli
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```
Result
----------------
hello-world
```

Poniższy przykład zawiera listę tagów w repozytorium **Hello-World** .

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Dane wyjściowe:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów, rejestr kontenerów i przechowywane tam obrazy kontenerów.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure, wypchnięto obraz kontenera do rejestru oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
