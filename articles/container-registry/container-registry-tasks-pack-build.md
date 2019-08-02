---
title: Tworzenie obrazu Azure Container Registry z poziomu aplikacji
description: Użyj polecenia AZ ACR Pack Build, aby skompilować obraz kontenera z aplikacji i wypchnąć do Azure Container Registry bez użycia pliku dockerfile.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/22/2019
ms.author: danlep
ms.openlocfilehash: 5100418651e24d74ad747e8c436ffce53c899a92
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68500899"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Kompilowanie i wypychanie obrazu z aplikacji przy użyciu natywnej Buildpack w chmurze

Polecenie interfejsu wiersza polecenia `az acr pack build` platformy Azure [`pack`](https://github.com/buildpack/pack) używa narzędzia CLI, z [Buildpacks](https://buildpacks.io/), do kompilowania aplikacji i wypychania jej obrazu do usługi Azure Container Registry. Ta funkcja udostępnia opcję szybkiego tworzenia obrazu kontenera na podstawie kodu źródłowego aplikacji w języku Node. js, Java i innych językach bez konieczności definiowania pliku dockerfile.

Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.70 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="use-the-build-command"></a>Korzystanie z polecenia Build

Aby skompilować i wypchnąć obraz kontenera za pomocą natywnej Buildpacks w chmurze, uruchom polecenie [AZ ACR Pack Build][az-acr-pack-build] . Podczas gdy polecenie [AZ ACR Build][az-acr-build] kompiluje i wypycha obraz ze źródła pliku dockerfile i powiązanego kodu, `az acr pack build` należy bezpośrednio określić drzewo źródła aplikacji.

Podczas uruchamiania `az acr pack build`należy określić co najmniej następujące elementy:

* Rejestr kontenerów platformy Azure, w którym jest uruchamiane polecenie
* Nazwa obrazu i tag dla obrazu uzyskanego
* Jedna z [obsługiwanych lokalizacji kontekstu](container-registry-tasks-overview.md#quick-task) dla zadań ACR, takich jak katalog lokalny, repozytorium GitHub lub zdalne plik tar
* Nazwa obrazu konstruktora Buildpack, `cloudfoundry/cnb:bionic`na przykład.  

`az acr pack build`obsługuje inne funkcje poleceń ACR Tasks, w tym [uruchamiania zmiennych](container-registry-tasks-reference-yaml.md#run-variables) i [dzienników uruchamiania zadań](container-registry-tasks-overview.md#view-task-logs) , które są przesyłane strumieniowo, a także zapisane do późniejszego pobrania.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Przykład: Kompilowanie obrazu Node. js za pomocą programu Cloud Foundry Builder

Poniższy przykład kompiluje obraz kontenera z aplikacji node. js w repozytorium [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) , używając `cloudfoundry/cnb:bionic` konstruktora:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ten przykład kompiluje `node-app` obraz `1.0` przy użyciu znacznika i wypchnięcie go do rejestru kontenerów *rejestru* . W tym miejscu docelowa Nazwa rejestru jest jawnie poprzedzona nazwą obrazu. Jeśli nie zostanie określony, adres URL rejestru zostanie automatycznie poprzedzony nazwą obrazu.

Dane wyjściowe polecenia pokazują postęp kompilowania i wypychania obrazu. 

Po pomyślnym skompilowaniu obrazu można uruchomić go przy użyciu platformy Docker, jeśli jest zainstalowany. Najpierw Zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Przejdź do `localhost:1337` ulubionej przeglądarki, aby wyświetlić przykładową aplikację internetową. Naciśnij `[Ctrl]+[C]` klawisz, aby zatrzymać kontener.

## <a name="example-build-java-image-with-heroku-builder"></a>Przykład: Tworzenie obrazu Java za pomocą konstruktora Heroku

Poniższy przykład kompiluje obraz kontenera z aplikacji Java w repozytorium [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) , używając `heroku/buildpacks:18` konstruktora:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ten przykład kompiluje `java-app` obraz oznaczony przy użyciu identyfikatora uruchomienia polecenia i wypchnięcie go do rejestru kontenerów *rejestru* .

`--pull` Parametr określa, że polecenie pobiera najnowszy obraz konstruktora, co jest konieczne, ponieważ obraz Heroku Builder nie jest buforowany przez ACR Tasks.

Dane wyjściowe polecenia pokazują postęp kompilowania i wypychania obrazu. 

Po pomyślnym skompilowaniu obrazu można uruchomić go przy użyciu platformy Docker, jeśli jest zainstalowany. Najpierw Zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz, zastępując tag obrazu *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Przejdź do `localhost:8080` ulubionej przeglądarki, aby wyświetlić przykładową aplikację internetową. Naciśnij `[Ctrl]+[C]` klawisz, aby zatrzymać kontener.


## <a name="next-steps"></a>Kolejne kroki

Po skompilowaniu i wypchnięciu obrazu kontenera `az acr pack build`za pomocą programu można wdrożyć go jak dowolny obraz w wybranym miejscu docelowym. Opcje wdrażania platformy Azure obejmują uruchamianie go w [App Service](../app-service/containers/tutorial-custom-docker-image.md) lub [usługi Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md).

Aby uzyskać więcej informacji na temat funkcji zadań ACR, zobacz [Automatyzacja kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
