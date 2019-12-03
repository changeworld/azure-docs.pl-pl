---
title: Ciągłej integracji/ciągłego wdrażania z niestandardowymi kontenerami systemu Linux
description: Dowiedz się, jak skonfigurować ciągłe wdrażanie do niestandardowego kontenera systemu Linux w Azure App Service. Ciągłe wdrażanie jest obsługiwane w przypadku usługi Docker Hub i ACR.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687631"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie za pomocą Web App for Containers

W tym samouczku skonfigurujesz ciągłe wdrażanie niestandardowego obrazu kontenera z zarządzanych [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repozytoriów lub usługi [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Włączanie ciągłego wdrażania za pomocą ACR

![Zrzut ekranu przedstawiający element webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wybierz opcję **App Service** po lewej stronie.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **pojedynczy kontener**
5. Wybierz **Azure Container Registry**
6. Wybierz **> ciągłego wdrażania na**
7. Wybierz pozycję **Zapisz** , aby włączyć ciągłe wdrażanie.

## <a name="use-the-acr-webhook"></a>Korzystanie z elementu webhook ACR

Po włączeniu ciągłego wdrażania można wyświetlić nowo utworzony element webhook na stronie Azure Container Registry elementów webhook.

![Zrzut ekranu przedstawiający element webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

W Container Registry kliknij pozycję elementy webhook, aby wyświetlić bieżące elementy webhook.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Włączanie ciągłego wdrażania za pomocą narzędzia Docker Hub (opcjonalnie)

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wybierz opcję **App Service** po lewej stronie.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **pojedynczy kontener**
5. Wybieranie **centrum platformy Docker**
6. Wybierz **> ciągłego wdrażania na**
7. Wybierz pozycję **Zapisz** , aby włączyć ciągłe wdrażanie.

![Zrzut ekranu ustawienia aplikacji](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Skopiuj adres URL elementu webhook. Aby dodać element webhook dla usługi Docker Hub, obserwuj elementy <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhook dla usługi Docker Hub</a>.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure App Service w systemie Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Tworzenie aplikacji sieci Web Ruby w App Service w systemie Linux](quickstart-ruby.md)
* [Wdróż aplikację sieci Web platformy Docker/go w Web App for Containers](quickstart-docker-go.md)
* [Azure App Service on Linux FAQ](./app-service-linux-faq.md) (Usługa Azure App Service w systemie Linux — często zadawane pytania)
* [Zarządzanie funkcją Web App for Containers za pomocą interfejsu wiersza polecenia platformy Azure](./app-service-linux-cli.md)
