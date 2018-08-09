---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Szybka nauka tworzenia rejestru prywatnego platformy Docker przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 78dc9eceba11ce07deb7fe0d10df1fea9cd74a75
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426126"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Szybki start: Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Container Registry to zarządzana usługa rejestru kontenerów platformy Docker używana do przechowywania prywatnych obrazów kontenerów Docker. W tym przewodniku szczegółowo opisano tworzenie wystąpienia usługi Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure, wypychanie obrazu kontenera do rejestru i na koniec wdrażanie kontenera z rejestru do usługi Azure Container Instances (ACI).

Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Musisz mieć również zainstalowane lokalnie środowisko Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym przewodniku Szybki start utworzysz rejestr w ramach jednostki *Podstawowa*. Usługa Azure Container Registry jest dostępna w ramach kilku różnych jednostek SKU, krótko opisanych w poniższej tabeli. Aby uzyskać szczegółowe informacji o każdej z nich, zobacz [Jednostki SKU rejestru kontenerów][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Utwórz wystąpienie usługi ACR za pomocą polecenia [az acr create][az-acr-create].

Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie użyto nazwy *myContainerRegistry007*. Zaktualizuj ją do unikatowej wartości.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po utworzeniu rejestru dane wyjściowe będą podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
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

W dalszej części tego przewodnika Szybki start wartość `<acrName>` jest używana jako symbol zastępczy nazwy rejestru kontenerów.

## <a name="log-in-to-acr"></a>Zaloguj się do usługi ACR

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do wystąpienia usługi ACR. Aby to zrobić, użyj polecenia [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Po ukończeniu polecenie zwraca komunikat `Login Succeeded`.

## <a name="push-image-to-acr"></a>Wypychanie obrazu do usługi ACR

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, uruchom następujące polecenie, aby ściągnąć istniejący obraz z usługi Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Zanim będzie można wypchnąć obraz do rejestru, musisz go otagować w pełni kwalifikowaną nazwą serwera logowania usługi ACR. Uruchom następujące polecenie, aby uzyskać pełną nazwę serwera logowania wystąpienia usługi ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element `<acrLoginServer>` nazwą serwera logowania wystąpienia usługi ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Na koniec użyj polecenia [docker push][docker-push], aby wypchnąć obraz do wystąpienia usługi ACR. Zastąp element `<acrLoginServer>` nazwą serwera logowania wystąpienia usługi ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Poniższy przykład wyświetla listę repozytoriów w rejestrze:

```azurecli
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```bash
Result
----------------
aci-helloworld
```

Poniższy przykład wyświetla listę tagów w repozytorium **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Dane wyjściowe:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Wdrażanie obrazu w usłudze ACI

W celu wdrożenia wystąpienia kontenera z utworzonego rejestru musisz podać podczas wdrażania poświadczenia rejestru. W scenariuszach ze środowiskiem produkcyjnym do uzyskiwania dostępu do rejestru kontenerów powinna być używana [jednostka usługi][container-registry-auth-aci], ale aby nie wydłużać tego przewodnika Szybki start, włącz administratora w swoim rejestrze za pomocą następującego polecenia:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Po włączeniu administratora nazwa użytkownika jest taka sama jak nazwa rejestru, a hasło można uzyskać za pomocą następującego polecenia:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Aby wdrożyć obraz kontenera przy użyciu 1 rdzenia procesora CPU i 1 GB pamięci, uruchom następujące polecenie. Zastąp elementy `<acrName>`, `<acrLoginServer>` i `<acrPassword>` wartościami uzyskanymi przy użyciu poprzednich poleceń.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Powinna pojawić się wstępna odpowiedź od usługi Azure Resource Manager ze szczegółowymi informacjami o kontenerze. Aby monitorować stan kontenera i sprawdzić, czy jest uruchomiony, powtórz polecenie [az container show][az-container-show]. Powinno to zająć mniej niż minutę.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Wyświetlanie aplikacji

Po pomyślnym wdrożenia w usłudze ACI uzyskaj nazwę FQDN kontenera za pomocą polecenia [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Przykładowe dane wyjściowe: `"aci-demo.eastus.azurecontainer.io"`

Aby wyświetlić działającą aplikację, przejdź do publicznego adresu IP w ulubionej przeglądarce.

![Aplikacja Hello World w przeglądarce][aci-app-browser]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, wystąpienie usługi ACR i wszystkie obrazy kontenerów nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzona została usługa Azure Container Registry z interfejsem wiersza polecenia platformy Azure, wypchnięto obraz kontenera do rejestru i uruchomiono jego wystąpienie za pośrednictwem usługi Azure Container Instances. Przejdź do samouczka usługi Azure Container Instances, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md