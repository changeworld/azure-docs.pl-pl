---
title: Zarządzanie aplikacją sieci Web dla kontenerów przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie aplikacją sieci Web dla kontenerów przy użyciu wiersza polecenia platformy Azure.
keywords: Usługa aplikacji Azure, aplikacji sieci web, interfejsu wiersza polecenia, linux, oss
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
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24105393"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Zarządzanie aplikacją sieci Web dla kontenerów przy użyciu wiersza polecenia platformy Azure

Za pomocą poleceń w tym artykule jest możliwe tworzenie i zarządzanie nimi w aplikacji sieci Web dla kontenerów używa interfejsu wiersza polecenia platformy Azure w wersji 2.0.
Można uruchomić przy użyciu nowej wersji interfejsu wiersza polecenia na dwa sposoby:

* [Instalowanie Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) na tym komputerze.
* Przy użyciu [powłoki chmury Azure (wersja zapoznawcza)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi aplikacji w systemie Linux

Aby utworzyć Plan usługi aplikacji systemu Linux, służy następujące polecenie:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Tworzenie niestandardowych kontenera Docker aplikacji sieci Web

Aby utworzyć aplikację sieci web i konfigurowanie go uruchomić niestandardowego kontenera Docker, służy następujące polecenie:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktywacja rejestrowania kontenera Docker

Aby aktywować rejestrowania kontenera Docker, służy następujące polecenie:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Zmiana niestandardowych kontenera Docker istniejącej aplikacji sieci Web dla kontenerów aplikacji

Aby zmienić utworzonej wcześniej aplikacji, z bieżącego obrazu Docker do nowego obrazu, można użyć następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Przy użyciu obrazy usługi Docker z rejestru prywatnych

Możesz skonfigurować aplikację, aby używać obrazów z rejestru prywatnych. Musisz podać adres url do rejestru, nazwę użytkownika i hasło. Można to osiągnąć przy użyciu następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Włącz ciągłego wdrożenia niestandardowe obrazy usługi Docker

Za pomocą następującego polecenia można włączyć funkcję CD i uzyskać adres url elementu webhook. Ten adres url, można skonfigurować repozytoriów DockerHub lub rejestru kontenera platformy Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Tworzenie aplikacji sieci Web dla aplikacji kontenerów przy użyciu jednej z naszych struktur wbudowanych środowiska wykonawczego

Aby utworzyć aplikację sieci Web PHP 5.6 kontenery aplikacji który, służy następujące polecenie.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Zmień wersję framework dla istniejącej aplikacji sieci Web dla kontenerów

Aby zmienić utworzonej wcześniej aplikacji, z bieżącej wersji framework do Node.js 6.11, można użyć następującego polecenia:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurowanie wdrożenia Git dla aplikacji sieci Web

Aby skonfigurować wdrożenia Git dla aplikacji, służy następujące polecenie:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure App Service w systemie Linux?](app-service-linux-intro.md)
* [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Powłoka chmury Azure (wersja zapoznawcza)](../../cloud-shell/overview.md)
* [Konfigurowanie środowisk w usłudze Azure App Service przejściowych](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie w aplikacji sieci Web dla kontenerów](app-service-linux-ci-cd.md)
