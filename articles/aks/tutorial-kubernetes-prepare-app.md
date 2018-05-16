---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — przygotowywanie aplikacji
description: Samouczek dotyczący usługi AKS — przygotowywanie aplikacji
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 50c302ddc7bad9cd2de666c1b99d1fbc6d5a62a8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-prepare-application-for-azure-kubernetes-service-aks"></a>Samouczek: przygotowywanie aplikacji dla usługi Azure Kubernetes Service (AKS)

Ten samouczek, część 1 z 8, obejmuje przygotowanie aplikacji z wieloma kontenerami do użycia w usłudze Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie aplikacji w lokalnym środowisku usługi Docker

Po zakończeniu następująca aplikacja będzie dostępna w lokalnym środowisku programistycznym.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

W kolejnych samouczkach obraz kontenera zostanie przekazany do usługi Azure Container Registry, a następnie uruchomiony w klastrze usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten samouczek zakłada, że masz podstawową wiedzę na temat bazowych koncepcji usługi Docker, takich jak kontenery, obrazy kontenerów i podstawowe polecenia usługi Docker. W razie potrzeby zapoznaj się z tematem [Get started with Docker (Rozpoczynanie pracy z platformą Docker)][docker-get-started], aby uzyskać podstawowe informacje na temat kontenerów.

Do ukończenia tego samouczka konieczne będzie środowisko programowania Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows] lub [Linux][docker-for-linux].

Usługa Azure Cloud Shell nie zawiera składników platformy Docker wymaganych do ukończenia każdego kroku w tym samouczku. Dlatego zalecamy używanie pełnego środowiska programistycznego usługi Docker.

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

W tym samouczku jest używana przykładowa prosta aplikacja do głosowania. Ta aplikacja składa się ze składnika internetowego frontonu oraz działającego na zapleczu wystąpienia usługi Redis. Składnik internetowy znajduje się w pakiecie niestandardowego obrazu kontenera. Wystąpienie usługi Redis używa niezmodyfikowanego obrazu z usługi Docker Hub.

Użyj narzędzia git, aby pobrać kopię tej aplikacji do swojego środowiska projektowego.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Zmień katalogi, aby pracować w sklonowanym katalogu.

```console
cd azure-voting-app-redis
```

W tym katalogu znajduje się kod źródłowy aplikacji, wstępnie utworzony plik redagowania usługi Docker i plik manifestu usługi Kubernetes. Te pliki są używane w całym zestawie samouczków.

## <a name="create-container-images"></a>Tworzenie obrazów kontenerów

Program [Docker Compose][docker-compose] umożliwia automatyzowanie tworzenia obrazów poza kontenerem i wdrażanie aplikacji wielokontenerowych.

Uruchom plik `docker-compose.yaml`, aby utworzyć obraz kontenera, pobrać obraz usługi Redis i uruchomić aplikację.

```console
docker-compose up -d
```

Po zakończeniu użyj polecenia [docker images][docker-images] w celu wyświetlenia utworzonych obrazów.

```console
docker images
```

Zwróć uwagę, że zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Uruchom polecenie [docker ps][docker-ps], aby wyświetlić uruchomione kontenery.

```console
docker ps
```

Dane wyjściowe:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Przejdź na adres http://localhost:8080, aby zobaczyć działającą aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Teraz, po zweryfikowaniu funkcjonalności aplikacji, uruchomione kontenery można zatrzymać i usunąć. Nie należy usuwać obrazów kontenera. Obraz `azure-vote-front` zostanie przekazany do wystąpienia usługi Azure Container Registry w następnym samouczku.

Uruchom następujące polecenie, aby zatrzymać uruchomione kontenery.

```console
docker-compose stop
```

Usuń zatrzymane kontenery i zasoby przy użyciu następującego polecenia.

```console
docker-compose down
```

Po zakończeniu będzie dostępny obraz kontenera zawierający aplikację Azure Vote.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przetestowano aplikację i obrazy kontenera utworzone dla aplikacji. Wykonano następujące czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie aplikacji w lokalnym środowisku usługi Docker

Przejdź do kolejnego samouczka, aby dowiedzieć się więcej o przechowywaniu obrazów kontenera w usłudze Azure Container Registry.

> [!div class="nextstepaction"]
> [Wypychanie obrazów do usługi Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md