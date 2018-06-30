---
title: Ciągłego wdrażania od rejestru kontenera Docker z aplikacji sieci Web dla kontenerów - Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować ciągłego wdrażania od rejestru kontenera Docker w aplikacji sieci Web dla kontenerów.
keywords: Usługa aplikacji Azure, linux, docker, acr, oss
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
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130969"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie w aplikacji sieci Web dla kontenerów

W tym samouczku, skonfiguruj ciągłe wdrażanie dla obrazu niestandardowego kontenera z zarządzanych [rejestru kontenera Azure](https://azure.microsoft.com/services/container-registry/) repozytoria lub [Centrum Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Włączanie funkcji ciągłego wdrażania

Włącz funkcję ciągłe wdrażanie przy użyciu [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i wykonując następujące polecenie:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

W [portalu Azure](https://portal.azure.com/), wybierz pozycję **usługi aplikacji** opcji w lewej części strony.

Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłego wdrażania Centrum Docker.

Na **kontenera Docker** wybierz pozycję **na**, a następnie wybierz **zapisać** umożliwiające ciągłego wdrażania.

![Zrzut ekranu przedstawiający ustawienia aplikacji](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Przygotowanie adresu URL elementu webhook

Uzyskaj za pomocą adresu URL elementu webhook [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i wykonując następujące polecenie:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Zanotuj adres URL elementu webhook. Będzie on potrzebny w następnej sekcji.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Można uzyskać z `publishingusername` i `publishingpwd` pobierając aplikacji sieci web Publikowanie profilu przy użyciu portalu Azure.

![Zrzut ekranu przedstawiający dodawanie elementu webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Dodawanie elementu webhook

Aby dodać elementu webhook, wykonaj kroki opisane w tych przewodnikach:

- [Azure rejestru kontenera](../../container-registry/container-registry-webhook.md) przy użyciu adresu URL elementu webhook
- [Elementów Webhook do Centrum Docker](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure App Service w systemie Linux](./app-service-linux-intro.md)
* [Rejestru kontenera platformy Azure](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Tworzenie aplikacji sieci web dopisków fonetycznych w usłudze App Service w systemie Linux](quickstart-ruby.md)
* [Wdrażanie aplikacji sieci web Docker/Przejdź w aplikacji sieci Web dla kontenerów](quickstart-docker-go.md)
* [Azure App Service on Linux FAQ](./app-service-linux-faq.md) (Usługa Azure App Service w systemie Linux — często zadawane pytania)
* [Zarządzanie aplikacją sieci Web dla kontenerów przy użyciu wiersza polecenia platformy Azure](./app-service-linux-cli.md)
