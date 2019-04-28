---
title: Tworzenie obrazów kontenerów w usłudze Service Fabric na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć obrazy kontenerów dla aplikacji obsługującej wiele kontenerów usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: c081a6296e1fae89f24a2c3ddb1ae66f7a3f94aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865293"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Samouczek: Tworzenie obrazów kontenerów w klastrze usługi Service Fabric systemu Linux

Ten samouczek jest częścią serii samouczków demonstrujących sposób korzystania z kontenerów w klastrze usługi Service Fabric w systemie Linux. W tym samouczku aplikacja obsługująca wiele kontenerów jest przygotowywana do użycia z usługą Service Fabric. W kolejnych samouczkach te obrazy są używane jako część aplikacji usługi Service Fabric. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Wdrażanie wystąpienia usługi Azure Container Registry (ACR)
> * Tagowanie obrazu kontenera na potrzeby usługi ACR
> * Przekazywanie obrazu do usługi ACR

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obrazów kontenerów dla usługi Service Fabric
> * [Kompilowanie i uruchamianie aplikacji usługi Service Fabric z kontenerami](service-fabric-tutorial-package-containers.md)
> * [Sposób obsługi trybu failover i skalowania w usłudze Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko projektowe systemu Linux skonfigurowane na potrzeby usługi Service Fabric. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](service-fabric-get-started-linux.md), aby skonfigurować środowisko systemu Linux.
* Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Wymagana jest również dostępna subskrypcja platformy Azure. Aby uzyskać więcej informacji dotyczących bezpłatnej wersji próbnej, przejdź [tutaj](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

W tym samouczku jest używana przykładowa aplikacja do głosowania. Ta aplikacja składa się ze składnika internetowego frontonu oraz działającego na zapleczu wystąpienia usługi Redis. Te składniki są spakowane w kontenerze obrazów.

Użyj narzędzia git, aby pobrać kopię tej aplikacji do swojego środowiska projektowego.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Rozwiązanie zawiera dwa foldery i plik „docker-compose.yml”. Folder „azure-vote” zawiera napisaną w języku Python usługę frontonu oraz plik Dockerfile używany do tworzenia obrazu. Katalog „Voting” zawiera wdrożony w klastrze pakiet aplikacji usługi Service Fabric. Te katalogi zawierają zasoby wymagane w tym samouczku.

## <a name="create-container-images"></a>Tworzenie obrazów kontenerów

Z poziomu katalogu **azure-vote** uruchom następujące polecenie, aby utworzyć obraz dla składnika internetowego frontonu. Do utworzenia obrazu to polecenie używa pliku Dockerfile znajdującego się w tym katalogu.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> W razie odmowy przyznania uprawnień postępuj zgodnie z [następującą](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) dokumentacją dotyczącą pracy z programem Docker bez uprawnień „sudo”.

Wykonanie tego polecenia może zająć trochę czasu, ponieważ wszystkie wymagane zależności muszą zostać pobrane z usługi Docker Hub. Po zakończeniu użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/) w celu wyświetlenia utworzonych obrazów.

```bash
docker images
```

Zwróć uwagę, że zostały pobrane lub utworzone dwa obrazy. Obraz *azure-vote-front* zawiera aplikację. Został on uzyskany z obrazu *python* z usługi Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Wdrażanie usługi Azure Container Registry

Najpierw uruchom polecenie **az login**, aby zalogować się do konta platformy Azure.

```bash
az login
```

Następnie użyj polecenia **az account**, aby wybrać subskrypcję używaną do utworzenia rejestru usługi Azure Container. Zamiast ciągu <subscription_id> wprowadź identyfikator swojej subskrypcji platformy Azure.

```bash
az account set --subscription <subscription_id>
```

W przypadku wdrażania usługi Azure Container Registry należy najpierw posiadać grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia **az group create**. W tym przykładzie grupa zasobów o nazwie *myResourceGroup* jest tworzona w regionie *westus*.

```bash
az group create --name <myResourceGroup> --location westus
```

Utwórz rejestr usługi Azure Container za pomocą polecenia **az acr create**. Zamiast ciągu \<acrName> wpisz nazwę rejestru kontenerów, który ma zostać utworzony w ramach Twojej subskrypcji. Ta nazwa może zawierać tylko znaki alfanumeryczne i musi być unikatowa.

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

W dalszej części tego samouczka wartość „acrName” jest używana jako symbol zastępczy wybranej nazwy rejestru kontenerów. Zanotuj tę wartość.

## <a name="log-in-to-your-container-registry"></a>Logowanie do rejestru kontenerów

Zaloguj się do swojego wystąpienia usługi ACR przed wypchnięciem do niego obrazów. Aby wykonać tę operację, użyj polecenia **az acr login**. Podaj unikatową nazwę nadaną rejestrowi kontenerów podczas jego tworzenia.

```bash
az acr login --name <acrName>
```

Polecenie zwraca komunikat „Logowanie pomyślne” po ukończeniu.

## <a name="tag-container-images"></a>Tagowanie obrazów kontenerów

Każdy obraz kontenera należy otagować za pomocą nazwy loginServer rejestru. Ten tag jest używany na potrzeby kierowania podczas wypychania obrazów kontenerów do rejestru obrazów.

Aby wyświetlić listę bieżących obrazów, użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Dane wyjściowe:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Aby uzyskać nazwę loginServer, uruchom następujące polecenie:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Spowoduje to wygenerowanie tabeli z następującymi wynikami. Ten wynik zostanie użyty do otagowania obrazu **azure-vote-front** przed jego wypchnięciem do rejestru kontenerów w następnym kroku.

```bash
Result
------------------
<acrName>.azurecr.io
```

Teraz otaguj obraz *azure-vote-front* za pomocą nazwy loginServer swojego rejestru kontenerów. Ponadto dodaj wartość `:v1` na końcu nazwy obrazu. Ten tag wskazuje wersję obrazu.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Po otagowaniu uruchom polecenie „docker images”, aby zweryfikować operację.

Dane wyjściowe:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Wypchnij obraz *azure-vote-front* do rejestru. 

Korzystając z następującego przykładu, zastąp nazwę loginServer usługi ACR nazwą loginServer z używanego środowiska.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Wykonanie poleceń docker push może potrwać kilka minut.

## <a name="list-images-in-registry"></a>Wyświetlanie listy obrazów w rejestrze

Aby zwrócić listę obrazów, które zostały wypchnięte do rejestru usługi Azure Container, użyj polecenia [az acr repository list](/cli/azure/acr/repository). Zaktualizuj polecenie nazwą wystąpienia usługi ACR.

```bash
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```bash
Result
----------------
azure-vote-front
```

Po ukończeniu tego samouczka obraz kontenera zostanie zapisany w prywatnym wystąpieniu usługi Azure Container Registry. W kolejnych samouczkach ten obraz zostanie wdrożony z usługi ACR do klastra usługi Service Fabric.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku aplikacja została ściągnięta z witryny GitHub, a obrazy kontenerów zostały utworzone i wypchnięte do rejestru. Wykonano następujące czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Wdrażanie wystąpienia usługi Azure Container Registry (ACR)
> * Tagowanie obrazu kontenera na potrzeby usługi ACR
> * Przekazywanie obrazu do usługi ACR

Przejdź do następnego samouczka, aby dowiedzieć się więcej o pakowaniu kontenerów do aplikacji usługi Service Fabric przy użyciu narzędzia Yeoman.

> [!div class="nextstepaction"]
> [Tworzenie pakietów kontenerów i wdrażanie ich jako aplikacji usługi Service Fabric](service-fabric-tutorial-package-containers.md)
