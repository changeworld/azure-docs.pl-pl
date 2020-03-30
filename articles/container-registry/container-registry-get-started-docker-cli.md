---
title: Wypychanie & ściąganie obrazu platformy Docker
description: Wypychanie i ściąganie obrazów platformy Docker do prywatnego rejestru kontenerów na platformie Azure za pomocą interfejsu wiersza polecenia platformy Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456357"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Wypchnij swój pierwszy obraz do prywatnego rejestru kontenerów platformy Docker za pomocą interfejsu wiersza polecenia platformy Docker

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy [Docker](https://hub.docker.com) i zarządza nimi podobnie, jak [koncentrator platformy Docker](https://hub.docker.com/) przechowuje publiczne obrazy platformy Docker. Do [logowania,](https://docs.docker.com/engine/reference/commandline/login/) [wypychania,](https://docs.docker.com/engine/reference/commandline/push/) [ściągania](https://docs.docker.com/engine/reference/commandline/pull/)i innych operacji w rejestrze kontenerów można użyć [interfejsu wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI).

W poniższych krokach można pobrać oficjalny [obraz Nginx](https://store.docker.com/images/nginx) z publicznego rejestru usługi Docker Hub, oznaczyć go dla prywatnego rejestru kontenerów platformy Azure, wypchnąć go do rejestru, a następnie pobrać go z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [witryny Azure portal](container-registry-get-started-portal.md) lub interfejsu [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI** — musi być również zainstalowany docker lokalnie. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Zaloguj się do rejestru

Istnieje [kilka sposobów uwierzytelniania w](container-registry-authentication.md) rejestrze kontenerów prywatnych. Zalecaną metodą podczas pracy w wierszu polecenia jest polecenie azure cli [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Na przykład, aby zalogować się do rejestru o nazwie *myregistry:*

```azurecli
az acr login --name myregistry
```

Możesz również zalogować się za pomocą [logowania docker](https://docs.docker.com/engine/reference/commandline/login/). Na przykład może mieć [przypisane jednostki usługi](container-registry-authentication.md#service-principal) do rejestru dla scenariusza automatyzacji. Po uruchomieniu następującego polecenia, interaktywnie podać appID jednostki usługi (nazwa użytkownika) i hasło po wyświetleniu monitu. Aby uzyskać najlepsze rozwiązania dotyczące zarządzania poświadczeniami logowania, zobacz odwołanie do polecenia [logowania docker:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Oba polecenia `Login Succeeded` zwracają po zakończeniu.

> [!TIP]
> Zawsze określ w pełni kwalifikowaną nazwę rejestru `docker login` (wszystkie małe litery) podczas używania i oznaczania obrazów do wypychania do rejestru. W przykładach w tym artykule w pełni kwalifikowana nazwa jest *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Pociągnij oficjalny obraz Nginx

Najpierw pociągnij publiczny obraz Nginx do komputera lokalnego.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Wykonaj następujące polecenie [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/run/) aby interaktywnie`-it`uruchomić lokalne wystąpienie kontenera Nginx ( ) na porcie 8080. Argument `--rm` określa, że kontener powinien zostać usunięty po zatrzymaniu go.

```
docker run -it --rm -p 8080:80 nginx
```

Przejdź do, aby `http://localhost:8080` wyświetlić domyślną stronę sieci Web obsługiwana przez Nginx w uruchomionym kontenerze. Powinna zostać wyświetlona strona podobna do następującej:

![Kontener Nginx na komputerze lokalnym](./media/container-registry-get-started-docker-cli/nginx.png)

Ponieważ kontener został uruchomiony `-it`interaktywnie za pomocą programu , po przejściu do niego w przeglądarce można wyświetlić dane wyjściowe serwera Nginx w wierszu polecenia.

Aby zatrzymać i wyjąć `Control` + `C`pojemnik, naciśnij klawisz .

## <a name="create-an-alias-of-the-image"></a>Tworzenie aliasu obrazu

Użyj [tagu docker,](https://docs.docker.com/engine/reference/commandline/tag/) aby utworzyć alias obrazu z w pełni kwalifikowaną ścieżką do rejestru. W tym przykładzie jest określana przestrzeń nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Aby uzyskać więcej informacji na temat tagowania za pomocą obszarów nazw, zobacz sekcję [Obszary nazw repozytorium](container-registry-best-practices.md#repository-namespaces) [w sekcji Najważniejsze wskazówki dotyczące rejestru kontenerów platformy Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Wypchnięcie obrazu do rejestru

Teraz, gdy masz oznaczone obrazu z w pełni kwalifikowaną ścieżkę do rejestru prywatnego, można wypchnąć go do rejestru z [docker push:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Ściągnięcie obrazu z rejestru

Użyj polecenia [ściągania docker,](https://docs.docker.com/engine/reference/commandline/pull/) aby wyciągnąć obraz z rejestru:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Uruchamianie kontenera Nginx

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/run/) aby uruchomić obraz pobrany z rejestru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Przejdź do, aby `http://localhost:8080` wyświetlić uruchomiony kontener.

Aby zatrzymać i wyjąć `Control` + `C`pojemnik, naciśnij klawisz .

## <a name="remove-the-image-optional"></a>Usuwanie obrazu (opcjonalnie)

Jeśli obraz Nginx nie jest już potrzebny, można go usunąć lokalnie za pomocą polecenia [rmi platformy docker.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Aby usunąć obrazy z rejestru kontenerów platformy Azure, można użyć polecenia azure cli [polecenia az repozytorium acr delete](/cli/azure/acr/repository#az-acr-repository-delete). Na przykład następujące polecenie usuwa manifest, do `samples/nginx:latest` którego odwołuje się znacznik, wszystkie unikatowe dane warstwy i wszystkie inne znaczniki odwołujące się do manifestu.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy, jesteś gotowy, aby rozpocząć korzystanie z rejestru! Na przykład wdrożyć obrazy kontenerów z rejestru do:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Opcjonalnie zainstaluj [rozszerzenie platformy Docker dla kodu programu Visual Studio](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie konta platformy [Azure,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) aby pracować z rejestrami kontenerów platformy Azure. Ściągaj i wypychaj obrazy do rejestru kontenerów platformy Azure lub uruchom zadania usługi ACR, wszystkie w ramach programu Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
