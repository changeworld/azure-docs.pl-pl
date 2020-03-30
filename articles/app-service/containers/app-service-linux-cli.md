---
title: Zarządzanie niestandardowymi kontenerami systemu Linux za pomocą interfejsu wiersza polecenia
description: Dowiedz się, jak zarządzać niestandardowymi kontenerami systemu Linux w usłudze Azure App Service za pomocą wiersza polecenia. Automatyzacja inicjowania obsługi administracyjnej lub konserwacji aplikacji.
keywords: azure app service, aplikacja internetowa, cli, Linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255925"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Zarządzanie funkcją Web App for Containers za pomocą interfejsu wiersza polecenia platformy Azure

Za pomocą poleceń w tym artykule można utworzyć aplikację sieci Web dla kontenerów i zarządzać nią przy użyciu interfejsu wiersza polecenia platformy Azure.
Możesz rozpocząć korzystanie z nowej wersji interfejsu wiersza polecenia na dwa sposoby:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na komputerze.
* Korzystanie z [usługi Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi aplikacji systemu Linux

Aby utworzyć plan usługi aplikacji systemu Linux, można użyć następującego polecenia:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Tworzenie niestandardowej aplikacji sieci Web kontenera platformy Docker

Aby utworzyć aplikację sieci web i skonfigurować ją do uruchamiania niestandardowego kontenera platformy Docker, można użyć następującego polecenia:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Uaktywnianie rejestrowania kontenerów platformy Docker

Aby aktywować rejestrowanie kontenera platformy Docker, można użyć następującego polecenia:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Zmienianie niestandardowego kontenera platformy Docker dla istniejącej aplikacji sieci Web dla kontenerów

Aby zmienić poprzednio utworzoną aplikację, z bieżącego obrazu platformy Docker na nowy obraz, można użyć następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Korzystanie z obrazów platformy Docker z rejestru prywatnego

Aplikację można skonfigurować do używania obrazów z rejestru prywatnego. Musisz podać adres URL rejestru, nazwę użytkownika i hasło. Można to osiągnąć za pomocą następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Włączanie ciągłych wdrożeń dla niestandardowych obrazów platformy Docker

Za pomocą następującego polecenia można włączyć funkcjonalność dysku CD i uzyskać adres URL elementu webhook. Ten adres URL może służyć do konfigurowania repozytorium usługi DockerHub lub usługi Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Tworzenie aplikacji sieci Web dla kontenerów przy użyciu jednej z naszych wbudowanych struktur środowiska wykonawczego

Aby utworzyć aplikację sieci Web PHP 5.6 dla kontenerów, która, można użyć następującego polecenia.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Zmienianie wersji struktury dla istniejącej aplikacji sieci Web dla kontenerów

Aby zmienić poprzednio utworzoną aplikację, z bieżącej wersji struktury na Node.js 6.11, można użyć następującego polecenia:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurowanie wdrożeń git dla aplikacji sieci Web

Aby skonfigurować wdrożenia Git dla aplikacji, możesz użyć następującego polecenia:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Azure App Service w systemie Linux?](app-service-linux-intro.md)
* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Usługa Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie za pomocą aplikacji sieci Web dla kontenerów](app-service-linux-ci-cd.md)
