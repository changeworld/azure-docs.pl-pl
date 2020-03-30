---
title: Wyt/CD do niestandardowych kontenerów systemu Linux
description: Dowiedz się, jak skonfigurować ciągłe wdrażanie niestandardowego kontenera systemu Linux w usłudze Azure App Service. Ciągłe wdrażanie jest obsługiwane dla centrów Docker Hub i ACR.
keywords: azure app service, Linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687631"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie za pomocą usługi Web App for Containers

W tym samouczku można skonfigurować ciągłe wdrażanie niestandardowego obrazu kontenera z zarządzanych repozytoriów [rejestru kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/) lub Centrum platformy [Docker.](https://hub.docker.com)

## <a name="enable-continuous-deployment-with-acr"></a>Włączanie ciągłego wdrażania za pomocą usługi ACR

![Zrzut ekranu przedstawiający element webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz opcję **Usługa app service** po lewej stronie strony.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **Pojedynczy kontener**
5. Wybierz **rejestr kontenerów platformy Azure**
6. Wybierz **opcję ciągłego wdrażania > włączone**
7. Wybierz **pozycję Zapisz,** aby włączyć ciągłe wdrażanie.

## <a name="use-the-acr-webhook"></a>Korzystanie z elementu webhook ACR

Po włączeniu ciągłego wdrażania można wyświetlić nowo utworzony element webhook na stronie elementów webhook rejestru kontenerów platformy Azure.

![Zrzut ekranu przedstawiający element webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

W rejestrze kontenerów kliknij elementy Webhook, aby wyświetlić bieżące elementy webhook.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Włącz ciągłe wdrażanie za pomocą Usługi Docker Hub (opcjonalnie)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz opcję **Usługa app service** po lewej stronie strony.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **Pojedynczy kontener**
5. Wybierz **Centrum platformy Docker**
6. Wybierz **opcję ciągłego wdrażania > włączone**
7. Wybierz **pozycję Zapisz,** aby włączyć ciągłe wdrażanie.

![Zrzut ekranu przedstawiający ustawienie aplikacji](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Skopiuj adres URL elementu webhook. Aby dodać element webhook dla centrum platformy Docker, postępuj zgodnie <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">z elementami webhook dla centrum platformy Docker</a>.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure App Service w systemie Linux](./app-service-linux-intro.md)
* [Rejestr kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Tworzenie aplikacji internetowej Ruby w usłudze App Service w systemie Linux](quickstart-ruby.md)
* [Wdrażanie aplikacji internetowej Docker/Go w funkcji Web App for Containers](quickstart-docker-go.md)
* [Azure App Service on Linux FAQ](./app-service-linux-faq.md) (Usługa Azure App Service w systemie Linux — często zadawane pytania)
* [Zarządzanie funkcją Web App for Containers za pomocą interfejsu wiersza polecenia platformy Azure](./app-service-linux-cli.md)
