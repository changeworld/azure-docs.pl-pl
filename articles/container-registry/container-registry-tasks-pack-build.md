---
title: Tworzenie obrazu za pomocą natywnego pakietu kompilacji w chmurze
description: Użyj polecenia kompilacji pakietu az acr do tworzenia obrazu kontenera z aplikacji i wypychania do rejestru kontenerów platformy Azure bez użycia pliku Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087078"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Tworzenie i wypychanie obrazu z aplikacji przy użyciu natywnego pakietu kompilacji w chmurze

Polecenie `az acr pack build` interfejsu wiersza [`pack`](https://github.com/buildpack/pack) polecenia platformy Azure używa narzędzia interfejsu wiersza polecenia, z [pakietów kompilacji,](https://buildpacks.io/)do tworzenia aplikacji i wypychania jej obrazu do rejestru kontenerów platformy Azure. Ta funkcja umożliwia szybkie tworzenie obrazu kontenera z kodu źródłowego aplikacji w node.js, Java i innych językach bez konieczności definiowania pliku Dockerfile.

Aby uruchomić przykłady w tym artykule, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.70 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="use-the-build-command"></a>Użyj polecenia kompilacji

Aby utworzyć i wypchnąć obraz kontenera przy użyciu natywnych pakietów kompilacji w chmurze, uruchom polecenie [kompilacji az acr pack.][az-acr-pack-build] Podczas gdy [polecenie kompilacji az acr][az-acr-build] tworzy i wypycha obraz ze źródła `az acr pack build` dockerfile i powiązanego kodu, a użytkownik określa bezpośrednio drzewo źródłowe aplikacji.

Po uruchomieniu `az acr pack build`należy określić co najmniej następujące informacje:

* Rejestr kontenerów platformy Azure, w którym jest uruchamiane polecenie
* Nazwa i znacznik obrazu wynikowego
* Jedna z [obsługiwanych lokalizacji kontekstu](container-registry-tasks-overview.md#context-locations) dla zadań usługi ACR, takich jak katalog lokalny, repozytorium GitHub lub zdalna tarball
* Nazwa obrazu konstruktora Buildpack nadaje się do aplikacji. Usługa Azure Container Registry buforuje `cloudfoundry/cnb:0.0.34-cflinuxfs3` obrazy konstruktora, takie jak szybsze kompilacje.  

`az acr pack build`obsługuje inne funkcje poleceń zadań ACR, w tym [uruchamianie zmiennych](container-registry-tasks-reference-yaml.md#run-variables) i [dzienników uruchamiania zadań,](container-registry-tasks-logs.md) które są przesyłane strumieniowo, a także zapisywane do późniejszego pobierania.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Przykład: Tworzenie obrazu node.js za pomocą konstruktora odlewniczego chmury

Poniższy przykład tworzy obraz kontenera z aplikacji Node.js w repozytorium [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) przy użyciu `cloudfoundry/cnb:0.0.34-cflinuxfs3` konstruktora. Ten konstruktor jest buforowany przez `--pull` usługę Azure Container Registry, więc parametr nie jest wymagany:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

W tym przykładzie `node-app` tworzy `1.0` obraz z tagiem i wypycha go do rejestru kontenerów *myregistry.* W tym przykładzie nazwa rejestru docelowego jest jawnie poprzedzana do nazwy obrazu. Jeśli nie zostanie określona, nazwa serwera logowania rejestru jest automatycznie dołączana do nazwy obrazu.

Wyjście polecenia pokazuje postęp tworzenia i wypychania obrazu. 

Po pomyślnym skuciu obrazu można go uruchomić za pomocą platformy Docker, jeśli jest zainstalowany. Najpierw zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Przejdź do `localhost:1337` ulubionej przeglądarki, aby zobaczyć przykładową aplikację internetową. Naciśnij, `[Ctrl]+[C]` aby zatrzymać pojemnik.

## <a name="example-build-java-image-with-heroku-builder"></a>Przykład: Tworzenie obrazu Java za pomocą konstruktora Heroku

Poniższy przykład tworzy obraz kontenera z aplikacji Java w repozytorium [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) przy użyciu konstruktora. `heroku/buildpacks:18` Parametr `--pull` określa, że polecenie powinno wyciągnąć najnowszy obraz konstruktora. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

W tym przykładzie tworzy `java-app` obraz oznaczony identyfikatorem uruchomienia polecenia i wypycha go do rejestru kontenerów *myregistry.*

Wyjście polecenia pokazuje postęp tworzenia i wypychania obrazu. 

Po pomyślnym skuciu obrazu można go uruchomić za pomocą platformy Docker, jeśli jest zainstalowany. Najpierw zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz, zastępując tag obrazu *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Przejdź do `localhost:8080` ulubionej przeglądarki, aby zobaczyć przykładową aplikację internetową. Naciśnij, `[Ctrl]+[C]` aby zatrzymać pojemnik.


## <a name="next-steps"></a>Następne kroki

Po utworzeniu i wypchnięciu obrazu kontenera za pomocą `az acr pack build`przycisku , można wdrożyć go jak każdy obraz do wybranego obiektu docelowego. Opcje wdrażania platformy Azure obejmują uruchamianie go między innymi w [usłudze App Service](../app-service/containers/tutorial-custom-docker-image.md) lub [usłudze Azure Kubernetes.](../aks/tutorial-kubernetes-deploy-cluster.md)

Aby uzyskać więcej informacji na temat funkcji zadań usługi ACR, zobacz [Automatyzuję kompilacje obrazów kontenerów i konserwację za pomocą zadań ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
