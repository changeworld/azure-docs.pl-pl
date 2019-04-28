---
title: Usługi App Service w systemie Linux — często zadawane pytania — Azure | Dokumentacja firmy Microsoft
description: Usługa Azure App Service w systemie Linux — często zadawane pytania.
keywords: azure app service, web app, faq, linux, oss, web app for containers, multi-container, multicontainer
services: app-service
documentationCenter: ''
author: yili
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: e3b6eed6f70eb2803ef4fa4e6b5d32fb0a4d843a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851157"
---
# <a name="azure-app-service-on-linux-faq"></a>Usługa Azure App Service w systemie Linux — często zadawane pytania

Wraz z wydaniem usługi App Service w systemie Linux pracujemy nad Dodawanie funkcji i wprowadzamy ulepszenia do naszej platformy. Ten artykuł zawiera odpowiedzi na pytania, że nasze prosili klienci nam ostatnio.

Jeśli masz pytania, komentarze, w tym artykule.

## <a name="built-in-images"></a>Wbudowane obrazy

**Chcę przeprowadzić rozwidlenie wbudowanych kontenerów platformy Docker, udostępnianych przez platformę. Gdzie można znaleźć te pliki?**

Możesz znaleźć wszystkie pliki Docker na [GitHub](https://github.com/azure-app-service). Możesz znaleźć wszystkie kontenery platformy Docker na [usługi Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Jakie są oczekiwane wartości dla sekcji plik startowy podczas konfigurowania stosu środowiska uruchomieniowego?**

| Stos     | Oczekiwana wartość                                                                |
|-----------|-------------------------------------------------------------------------------|
| Java SE   | polecenie, aby uruchomić usługi `.jar` aplikacji                                    |
| Tomcat    | Lokalizacja skryptu, aby wykonać wszystkie konfiguracje dla aplikacji          |
| Node.js   | pliku konfiguracji PM2 lub pliku skryptu                                |
| .Net Core | nazwę skompilowanej biblioteki DLL jako `dotnet <myapp>.dll`                                 |
| Ruby      | Skrypt języka Ruby, który chcesz zainicjować swoją aplikację przy użyciu                     |

## <a name="management"></a>Zarządzanie

**Co się stanie po naciśnięciu przycisku ponownego uruchomienia w witrynie Azure portal?**

Ta akcja jest taka sama jak ponowne uruchomienie platformy Docker.

**Czy można używać Secure Shell (SSH), aby nawiązać połączenie maszyny wirtualnej kontenera aplikacji (VM)?**

Tak, możesz to zrobić za pośrednictwem witryny management (SCM) kontroli źródła.

> [!NOTE]
> Możesz również nawiązać połączenie z kontenerem aplikacji bezpośrednio z lokalnego komputera deweloperskiego przy użyciu protokołu SSH, SFTP lub programu Visual Studio Code (na potrzeby debugowania na żywo aplikacji Node.js). Aby uzyskać więcej informacji, zobacz [Zdalne debugowanie i protokół SSH w usłudze App Service w systemie Linux](https://aka.ms/linux-debug).
>

**Jak utworzyć plan usługi App Service w systemie Linux przy użyciu zestawu SDK lub szablonu usługi Azure Resource Manager?**

Należy ustawić **zarezerwowanych** pole app service w celu *true*.

## <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

**Moja aplikacja sieci web nadal używa stary obraz kontenera Docker po po aktualizacji obraz w usłudze Docker Hub. Obsługują ciągłą integrację i wdrażanie kontenerów niestandardowych?**

Tak, aby skonfigurować ciągłej integracji/ciągłego wdrażania dla usługi Azure Container Registry lub DockerHub, następujące [ciągłe wdrażanie za pomocą usługi Web App for Containers](./app-service-linux-ci-cd.md). Dla prywatnych rejestrów możesz odświeżyć kontenera przez zatrzymanie i uruchomienie następnie aplikację sieci web. Lub można zmienić lub dodać ustawienie aplikacji z fikcyjnymi, aby wymusić odświeżenie kontenera.

**Tymczasowym są obsługiwane?**

Tak.

**Można użyć *WebDeploy/MSDeploy* Aby wdrożyć aplikację sieci web?**

Tak, musisz ustawić aplikacji nosi nazwę `WEBSITE_WEBDEPLOY_USE_SCM` do *false*.

**Wdrożenie usługi Git w mojej aplikacji zakończy się niepowodzeniem, podczas korzystania z aplikacji sieci web systemu Linux. Jak można obejść ten problem?**

W przypadku niepowodzenia wdrożenia narzędzia Git do aplikacji sieci web w systemie Linux, należy wybrać jedną z następujących opcji wdrażania kodu aplikacji:

- Użyj funkcji ciągłego dostarczania (wersja zapoznawcza): Kod źródłowy aplikacji można przechowywać w repozytorium DevOps Git platformy Azure lub w repozytorium GitHub do używania ciągłego dostarczania platformy Azure. Aby uzyskać więcej informacji, zobacz [jak skonfigurować ciągłe dostarczanie dla aplikacji sieci web w systemie Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Użyj [ZIP wdrażania interfejsu API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Aby użyć tego interfejsu API [SSH w aplikacji sieci web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) i przejdź do folderu, w której chcesz wdrożyć swój kod. Uruchom następujący kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Jeśli wystąpi błąd, który `curl` polecenia nie zostanie znaleziony, upewnij się, zainstaluj narzędzie curl, używając `apt-get install curl` przed uruchomieniem poprzedniego `curl` polecenia.

## <a name="language-support"></a>Obsługa języków

**Chcę użyć gniazda sieci web w mojej aplikacji Node.js, wszystkie ustawienia specjalne lub konfiguracje, aby ustawić?**

Tak, wyłącz `perMessageDeflate` w kodzie po stronie serwera Node.js. Na przykład jeśli używasz biblioteki socket.io należy użyć następującego kodu:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Nie skompilowanego aplikacje platformy .NET Core są obsługiwane?**

Tak.

**Composer (kompozytor) są obsługiwane jako Menedżer zależności dla aplikacji PHP?**

Tak, podczas wdrażania usługi Git, Kudu powinna wykryć czy wdrażasz aplikację PHP (dzięki obecności pliku composer.lock) i Kudu następnie spowoduje wyzwolenie instalacji Composer (kompozytor).

## <a name="custom-containers"></a>Kontenerów niestandardowych

**Korzystam z własnego kontenera niestandardowego. Chcę, aby platformy w celu instalowania udziału SMB do `/home/` katalogu.**

Możesz to zrobić, ustawiając `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienia aplikacji *true*. Należy pamiętać, że to spowoduje ponowne uruchomienia kontenera podczas magazynu platformy przechodzi przez zmianę.

>[!NOTE]
>Jeśli `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie jest nieokreślona lub *false*, `/home/` katalogu nie będą udostępniane w wystąpieniach skalowania i pliki, które są zapisywane istnieje nie zostaną utrwalone przez ponowne uruchomienie.

**Kontener niestandardowy zajmuje dużo czasu do uruchomienia, a platforma powoduje ponowne uruchomienie kontenera przed zakończeniem uruchamiania.**

Możesz skonfigurować ilość czasu oczekiwania platformę przed ponownym uruchomieniu kontenera. Aby to zrobić, należy ustawić `WEBSITES_CONTAINER_START_TIME_LIMIT` wartość, która ma ustawienie aplikacji. Wartość domyślna to 230 sekund, a wartość maksymalna to 1800 sekund.

**Co to jest format adresu URL prywatnego rejestru serwera?**

Podaj adres URL rejestru pełną, w tym `http://` lub `https://`.

**Co to jest format nazwy obrazu w opcji rejestru prywatnego?**

Dodaj nazwę pełnego obrazu, łącznie z adresem URL prywatnego rejestru (na przykład myacr.azurecr.io/dotnet:latest). Nazwy, które użyć portu niestandardowego obrazu [nie może zostać wprowadzone za pośrednictwem portalu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Aby ustawić `docker-custom-image-name`, użyj [ `az` narzędzia wiersza polecenia](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Czy mogę udostępnić więcej niż jeden port w mojej niestandardowego obrazu kontenera?**

Obecnie obsługujemy udostępnianie więcej niż jeden port.

**Czy mogę przenieść mój własny magazyn**

Tak, [model dostarczania własnego magazynu](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) jest w wersji zapoznawczej.

**Dlaczego nie można przeglądać Moje niestandardowego kontenera pliku systemu lub uruchamianie procesów z witryny SCM?**

Witryny SCM działa w oddzielnym kontenera. Nie można sprawdzić plik systemu lub uruchamianie procesów kontenera aplikacji.

**Niestandardowe kontener nasłuchuje port inny niż 80. Jak skonfigurować aplikację, aby kierować żądania do tego portu**

Mamy automatyczne wykrywanie portu. Można również określić, że aplikacja nosi nazwę *WEBSITES_PORT* i nadaj mu wartość numeru portu oczekiwane. Wcześniej platformie, z której *portu* ustawienia aplikacji. Planujemy zastąpić to ustawienie aplikacji oraz używanie *WEBSITES_PORT* wyłącznie.

**Należy zaimplementować protokół HTTPS w mojej niestandardowego kontenera?**

Nie, platforma obsługuje zakończenia połączenia HTTPS w udostępnionym frontonów.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Tworzenia wielu kontenerów przy użyciu rozwiązania Docker i Kubernetes

**Jak skonfigurować usługi Azure Container Registry (ACR) do użycia z wieloma kontenerami?**

Aby można było używać usługi ACR z wieloma kontenerami **wszystkie obrazy kontenerów** musi być hostowane na tym samym serwerze rejestru ACR. Gdy są one na tym samym serwerze rejestru, należy utworzyć ustawienia aplikacji, a następnie zaktualizuj plik konfiguracji narzędzia Docker Compose lub Kubernetes, aby zawierał on nazwę obrazu usługi ACR.

Utwórz następujące ustawienia aplikacji:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (pełny adres URL, np: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (Włączanie dostępu administratora w ustawieniach rejestru Azure container Registry)

W pliku konfiguracji należy odwoływać się jak w poniższym przykładzie obraz ACR:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak wiadomo, które kontener jest dostępny przez internet?**

- Tylko jeden kontener może być otwarte na potrzeby dostępu
- Tylko port 80 i 8080 jest dostępny (ujawnionych portów)

Poniżej przedstawiono zasady określające, kontener, który jest dostępny — w kolejności priorytetu:

- Ustawienie aplikacji `WEBSITES_WEB_CONTAINER_NAME` należy ustawić nazwę kontenera
- Pierwszy kontener, aby zdefiniować port 80 lub 8080
- Jeśli żadna z powyższych nie jest spełniony, pierwszego kontenera, które są zdefiniowane w pliku będzie dostępny (udostępniane)

## <a name="pricing-and-sla"></a>Cennik i umowy SLA

**Co to jest cennik, skoro usługa stanie się ogólnie dostępna?**

Opłaty są naliczane normalne ceny usługi Azure App Service przez liczbę godzin, w których Twoja aplikacja jest uruchamiana.

## <a name="other-questions"></a>Inne pytania

**Co to są obsługiwane znaki nazwy ustawień aplikacji?**

Dla ustawienia aplikacji, można użyć tylko litery (A – Z, a – z), cyfry (0 – 9) i znak podkreślenia (_).

**Gdzie mogę uzyskać nowe funkcje**

Możesz przesłać swój pomysł na [forum opinii w aplikacji sieci Web](https://aka.ms/webapps-uservoice). Dodaj "[Linux]" jako tytuł Twojego pomysłu.

## <a name="next-steps"></a>Kolejne kroki

- [Co to jest Azure App Service w systemie Linux?](app-service-linux-intro.md)
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ciągłe wdrażanie za pomocą aplikacji Web App for Containers](./app-service-linux-ci-cd.md)
