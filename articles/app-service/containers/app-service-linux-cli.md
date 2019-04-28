---
title: Zarządzanie aplikacją internetową dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Zarządzanie aplikacją internetową dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure.
keywords: Usługa Azure app service, aplikacji sieci web, interfejsu wiersza polecenia, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 21f6963fbaada4524f27602454d38e7252a5e8b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850088"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Zarządzanie aplikacją internetową dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure

Za pomocą poleceń, w tym artykule, których można tworzyć i zarządzać nimi Web App for Containers przy użyciu wiersza polecenia platformy Azure.
Można uruchomić przy użyciu nowej wersji interfejsu wiersza polecenia na dwa sposoby:

* [Instalowanie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na swojej maszynie.
* Za pomocą [usługi Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service w systemie Linux

Aby utworzyć Linux Plan usługi App Service, służy następujące polecenie:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Tworzenie niestandardowego kontenera Docker aplikacji sieci Web

Aby utworzyć aplikację sieci web i skonfigurowania go do uruchamiania niestandardowego kontenera Docker, służy następujące polecenie:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktywuj rejestrowanie kontenerów Docker

Aby aktywować rejestrowanie kontenerów Docker, służy następujące polecenie:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Zmiany niestandardowego kontenera Docker do istniejącej aplikacji sieci Web dla aplikacji kontenerów

Aby zmienić wcześniej utworzoną aplikację, z bieżącego obrazu platformy Docker do nowego obrazu, służy następujące polecenie:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Przy użyciu obrazów platformy Docker z rejestru prywatnego

Można skonfigurować aplikację tak, aby używać obrazów z rejestru prywatnego. Musisz podać adres url dla rejestru, nazwa użytkownika i hasło. Można to osiągnąć, używając następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Włącz ciągłe wdrażanie niestandardowych obrazów platformy Docker

Za pomocą następującego polecenia można włączyć funkcji ciągłego Dostarczania i uzyskać adres url elementu webhook. Ten adres url można skonfigurować repozytoriów witryny DockerHub lub usługi Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Tworzenie aplikacji internetowej dla kontenerów aplikacji przy użyciu jednego z naszych platform wbudowane środowisko uruchomieniowe

Utworzyć aplikację sieci Web PHP 5.6 dla kontenerów aplikacji, można użyć następującego polecenia.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Zmień wersję framework dla istniejącej aplikacji sieci Web dla aplikacji kontenerów

Aby zmienić wcześniej utworzoną aplikację, z bieżącej wersji framework Node.js 6.11, służy następujące polecenie:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurowanie wdrożenia Git dla aplikacji sieci Web

Aby skonfigurować wdrożenia Git dla aplikacji, służy następujące polecenie:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Azure App Service w systemie Linux?](app-service-linux-intro.md)
* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Usługi Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie za pomocą aplikacji Web App for Containers](app-service-linux-ci-cd.md)
