---
title: Ciągłe wdrażanie z rejestru kontenerów platformy Docker za pomocą usługi Web App for Containers — Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować ciągłe wdrażanie z rejestru kontenerów platformy Docker w sieci Web App for Containers.
keywords: Usługa Azure app service, linux, docker, rejestru Azure container Registry, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 20ca63b7126a6800538129115ff339308c11d8c5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867029"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie za pomocą usługi Web App for Containers

W ramach tego samouczka, możesz skonfigurować ciągłe wdrażanie dla niestandardowego obrazu kontenera z zarządzanego [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repozytoriów lub [usługi Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Włączanie funkcji ciągłego wdrażania

Włącz funkcję ciągłego wdrażania za pomocą [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i wykonując następujące polecenie:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **usługi App Service** opcji w lewej części strony.

Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie w usłudze Docker Hub.

Na **ustawienia kontenera** wybierz opcję **na**, a następnie wybierz pozycję **Zapisz** włączyć wdrażanie ciągłe.

![Zrzut ekranu przedstawiający ustawienie aplikacji](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Przygotowanie adresu URL elementu webhook

Uzyskiwanie adresu URL elementu webhook przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i wykonując następujące polecenie:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Zanotuj adres URL elementu webhook. Będzie potrzebny w następnej sekcji.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Można uzyskać z `publishingusername` i `publishingpwd` , pobierając aplikację sieci web Publikowanie profilu przy użyciu witryny Azure portal.

![Zrzut ekranu przedstawiający dodawanie elementu webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Dodaj element webhook

Aby dodać element webhook, wykonaj kroki opisane w tych przewodników:

- [Usługa Azure Container Registry](../../container-registry/container-registry-webhook.md) przy użyciu adresu URL elementu webhook
- [Elementy Webhook dla usługi Docker Hub](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure App Service w systemie Linux](./app-service-linux-intro.md)
* [Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Tworzenie aplikacji internetowej języka Ruby w usłudze App Service w systemie Linux](quickstart-ruby.md)
* [Wdrażanie aplikacji internetowej Docker/Go w sieci Web App for Containers](quickstart-docker-go.md)
* [Azure App Service on Linux FAQ](./app-service-linux-faq.md) (Usługa Azure App Service w systemie Linux — często zadawane pytania)
* [Zarządzanie aplikacją internetową dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure](./app-service-linux-cli.md)
