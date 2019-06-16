---
title: Ciągłe wdrażanie za pomocą usługi Web App for Containers — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Jak skonfigurować ciągłe wdrażanie w sieci Web App for Containers.
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
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60852523"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie za pomocą usługi Web App for Containers

W ramach tego samouczka, możesz skonfigurować ciągłe wdrażanie dla niestandardowego obrazu kontenera z zarządzanego [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repozytoriów lub [usługi Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Włącz ciągłe wdrażanie za pomocą rejestru Azure container Registry

![Zrzut ekranu usługi ACR elementu webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi App Service** opcji w lewej części strony.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na **ustawienia kontenera** wybierz opcję **jeden kontener**
5. Wybierz **usługi Azure Container Registry**
6. Wybierz **ciągłe wdrażanie > na**
7. Wybierz **Zapisz** włączyć wdrażanie ciągłe.

## <a name="use-the-acr-webhook"></a>Użyj elementu webhook rekordu ACR

Po włączeniu ciągłe wdrażanie nowo utworzony element webhook można wyświetlić na stronie elementy webhook usługi Azure Container Registry.

![Zrzut ekranu usługi ACR elementu webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

W rejestrze kontenerów kliknij elementy Webhook, aby wyświetlić bieżące elementy webhook.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Włącz ciągłe wdrażanie za pomocą usługi Docker Hub (opcjonalnie)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi App Service** opcji w lewej części strony.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na **ustawienia kontenera** wybierz opcję **jeden kontener**
5. Wybierz **usługi Docker Hub**
6. Wybierz **ciągłe wdrażanie > na**
7. Wybierz **Zapisz** włączyć wdrażanie ciągłe.

![Zrzut ekranu przedstawiający ustawienie aplikacji](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Skopiuj adres URL elementu Webhook. Aby dodać element webhook usługi Docker Hub, wykonaj <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">elementy webhook dla usługi Docker Hub</a>.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure App Service w systemie Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Tworzenie aplikacji internetowej języka Ruby w usłudze App Service w systemie Linux](quickstart-ruby.md)
* [Wdrażanie aplikacji internetowej Docker/Go w sieci Web App for Containers](quickstart-docker-go.md)
* [Azure App Service on Linux FAQ](./app-service-linux-faq.md) (Usługa Azure App Service w systemie Linux — często zadawane pytania)
* [Zarządzanie funkcją Web App for Containers za pomocą interfejsu wiersza polecenia platformy Azure](./app-service-linux-cli.md)
