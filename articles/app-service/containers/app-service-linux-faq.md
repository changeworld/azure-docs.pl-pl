---
title: Uruchamianie kontenerów wbudowanych — często zadawane pytania
description: Znajdź odpowiedzi na często zadawane pytania dotyczące wbudowanych kontenerów systemu Linux w usłudze Azure App Service.
keywords: usługa aplikacji azure, aplikacja internetowa, faq, Linux, oss, aplikacja internetowa dla kontenerów, wielokontener, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245824"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service on Linux FAQ (Usługa Azure App Service w systemie Linux — często zadawane pytania)

Wraz z wydaniem usługi App Service w systemie Linux pracujemy nad dodawaniem funkcji i wprowadzaniem ulepszeń do naszej platformy. Ten artykuł zawiera odpowiedzi na pytania, które nasi klienci zadają nam ostatnio.

Jeśli masz pytanie, skomentuj ten artykuł.

## <a name="built-in-images"></a>Wbudowane obrazy

**Chcę rozwidlić wbudowane kontenery platformy Docker, które zapewnia platforma. Gdzie mogę znaleźć te pliki?**

Wszystkie pliki platformy Docker można znaleźć na [gitHub](https://github.com/azure-app-service). Wszystkie kontenery platformy Docker można znaleźć w centrum [platformy Docker](https://hub.docker.com/u/appsvc/)Hub .

<a id="#startup-file"></a>

**Jakie są oczekiwane wartości dla sekcji Plik startowy podczas konfigurowania stosu środowiska wykonawczego?**

| Stos           | Wartość oczekiwana                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | polecenie uruchomienia aplikacji JAR (na `java -jar /home/site/wwwroot/app.jar --server.port=80`przykład) |
| Tomcat          | lokalizacja skryptu w celu wykonania niezbędnych konfiguracji `/home/site/deployments/tools/startup_script.sh`(na przykład)          |
| Node.js         | pliku konfiguracyjnego PM2 lub pliku skryptu                                |
| .NET Core       | skompilowaną nazwę biblioteki DLL jako`dotnet <myapp>.dll`                                 |
| Ruby            | skrypt Ruby, który chcesz zainicjować aplikację za pomocą                     |

Te polecenia lub skrypty są wykonywane po uruchomieniu wbudowanego kontenera platformy Docker, ale przed rozpoczęciem kodu aplikacji.

## <a name="management"></a>Zarządzanie

**Co się stanie po naciśnięciu przycisku ponownego uruchomienia w witrynie Azure portal?**

Ta akcja jest taka sama jak ponowne uruchomienie platformy Docker.

**Czy można używać secure shell (SSH) do łączenia się z maszyną wirtualną kontenera aplikacji (VM)?**

Tak, można to zrobić za pośrednictwem witryny zarządzania kontrolą źródła (SCM).

> [!NOTE]
> Możesz również nawiązać połączenie z kontenerem aplikacji bezpośrednio z lokalnego komputera deweloperskiego przy użyciu protokołu SSH, SFTP lub programu Visual Studio Code (na potrzeby debugowania na żywo aplikacji Node.js). Aby uzyskać więcej informacji, zobacz [Zdalne debugowanie i protokół SSH w usłudze App Service w systemie Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Jak utworzyć plan usługi aplikacji systemu Linux za pomocą zestawu SDK lub szablonu usługi Azure Resource Manager?**

Ustaw **pole zarezerwowane** usługi aplikacji na *true*.

## <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

**Moja aplikacja internetowa nadal używa starego obrazu kontenera platformy Docker po zaktualizowaniu obrazu w centrum platformy Docker Hub. Czy obsługujesz ciągłą integrację i wdrażanie kontenerów niestandardowych?**

Tak, aby skonfigurować ciągłą integrację/wdrażanie dla usługi Azure Container Registry lub DockerHub, wykonując [ciągłe wdrażanie za pomocą aplikacji sieci Web dla kontenerów.](./app-service-linux-ci-cd.md) W przypadku rejestrów prywatnych można odświeżyć kontener, zatrzymując, a następnie uruchamiając aplikację sieci web. Można też zmienić lub dodać fikcyjne ustawienie aplikacji, aby wymusić odświeżenie kontenera.

**Czy obsługujesz środowiska przejściowe?**

Tak.

**Czy mogę użyć *webdeploy/MSDeploy* do wdrożenia mojej aplikacji internetowej?**

Tak, musisz ustawić ustawienie aplikacji `WEBSITE_WEBDEPLOY_USE_SCM` nazywane *false*.

**Git wdrożenia mojej aplikacji kończy się niepowodzeniem podczas korzystania z aplikacji internetowej Linux. Jak mogę obejść ten problem?**

Jeśli wdrożenie git nie powiedzie się w aplikacji sieci Web systemu Linux, wybierz jedną z następujących opcji, aby wdrożyć kod aplikacji:

- Użyj funkcji ciągłego dostarczania (wersja zapoznawcza): możesz przechowywać kod źródłowy aplikacji w repozytorium Usługi Azure DevOps Git lub repozytorium GitHub, aby używać ciągłego dostarczania platformy Azure. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować ciągłe dostarczanie dla aplikacji sieci Web Systemu Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Użyj [interfejsu API wdrażania kodu zip:](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)Aby użyć tego interfejsu API, [SSH do aplikacji sieci web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) i przejdź do folderu, w którym chcesz wdrożyć kod. Uruchom następujący kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Jeśli zostanie wyświetlony błąd, że `curl` polecenie nie został znaleziony, `apt-get install curl` upewnij się, `curl` że instalujesz curl przy użyciu przed uruchomieniem poprzedniego polecenia.

## <a name="language-support"></a>Obsługa języków

**Chcę używać gniazd internetowych w mojej aplikacji Node.js, żadnych specjalnych ustawień lub konfiguracji do ustawienia?**

Tak, `perMessageDeflate` wyłącz kod Node.js po stronie serwera. Na przykład, jeśli używasz socket.io, użyj następującego kodu:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Czy obsługujesz niekompilowane aplikacje .NET Core?**

Tak.

**Czy obsługujesz Composer jako menedżera zależności dla aplikacji PHP?**

Tak, podczas wdrożenia Git, Kudu powinien wykryć, że wdrażasz aplikację PHP (dzięki obecności pliku composer.lock), a Kudu uruchomi instalację kompozytora.

## <a name="custom-containers"></a>Kontenery niestandardowe

**Używam własnego kontenera niestandardowego. Chcę, aby platforma zamontować `/home/` udział SMB w katalogu.**

Jeśli `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie jest **nieokreślone** lub `/home/` ustawione na *true,* katalog **będzie współużytkowany** w różnych wystąpieniach skali, a zapisane pliki **będą zachowywać się** przez ponowne uruchomienie. Jawnie `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie *false* spowoduje wyłączenie instalacji.

**Mój kontener niestandardowy zajmuje dużo czasu, aby rozpocząć, a platforma uruchamia ponownie kontenera przed zakończeniem uruchamiania.**

Można skonfigurować czas, przez jaki platforma będzie czekać, zanim uruchomi ponownie kontener. Aby to zrobić, `WEBSITES_CONTAINER_START_TIME_LIMIT` ustaw ustawienie aplikacji na żądaną wartość. Wartość domyślna to 230 sekund, a maksymalna wartość to 1800 sekund.

**Jaki jest format adresu URL prywatnego serwera rejestru?**

Podaj pełny adres `http://` `https://`URL rejestru, w tym lub .

**Jaki jest format nazwy obrazu w opcji rejestru prywatnego?**

Dodaj pełną nazwę obrazu, w tym adres URL rejestru prywatnego (na przykład myacr.azurecr.io/dotnet:latest). Nazwy obrazów korzystające z portu niestandardowego [nie mogą być wprowadzane za pośrednictwem portalu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Aby `docker-custom-image-name`ustawić , użyj [ `az` narzędzia wiersza polecenia](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Czy mogę udostępnić więcej niż jeden port na obrazie kontenera niestandardowego?**

Nie obsługujemy ujawniania więcej niż jednego portu.

**Czy mogę zabrać ze sobą własną pamięć masową?**

Tak, [przynieś własną pamięć masową](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) w wersji zapoznawczej.

**Dlaczego nie mogę przeglądać niestandardowego systemu plików kontenera lub uruchamiać procesów z witryny SCM?**

Witryna SCM działa w oddzielnym kontenerze. Nie można sprawdzić systemu plików ani uruchomionych procesów kontenera aplikacji.

**Mój kontener niestandardowy nasłuchuje portu innego niż port 80. Jak skonfigurować aplikację do kierowania żądań do tego portu?**

Mamy automatyczne wykrywanie portów. Można również określić ustawienie aplikacji o nazwie *WEBSITES_PORT* i nadać mu wartość oczekiwanego numeru portu. Wcześniej platforma używała ustawienia aplikacji *PORT.* Planujemy przestarzałe to ustawienie aplikacji i używać *wyłącznie WEBSITES_PORT.*

**Czy muszę zaimplementować protokół HTTPS w kontenerze niestandardowym?**

Nie, platforma obsługuje zakończenie protokołu HTTPS na udostępnionych frontach.

## <a name="multi-container-with-docker-compose"></a>Wielokontensja z docker compose

**Jak skonfigurować usługę Azure Container Registry (ACR) do używania z wieloma kontenerami?**

Aby używać usługi ACR z wieloma kontenerami, **wszystkie obrazy kontenerów** muszą być hostowane na tym samym serwerze rejestru ACR. Gdy znajdują się one na tym samym serwerze rejestru, należy utworzyć ustawienia aplikacji, a następnie zaktualizować plik konfiguracyjny docker compose, aby uwzględnić nazwę obrazu ACR.

Utwórz następujące ustawienia aplikacji:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (pełny adres URL, `https://<server-name>.azurecr.io`np.)
- DOCKER_REGISTRY_SERVER_PASSWORD (włącz dostęp administratora w ustawieniach ACR)

W pliku konfiguracyjnym odwołaj się do obrazu ACR, tak jak w poniższym przykładzie:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Skąd mam wiedzieć, który kontener jest dostępny w Internecie?**

- Tylko jeden kontener może być otwarty dla dostępu
- Dostępne są tylko porty 80 i 8080 (porty narażone)

Oto zasady określania, który kontener jest dostępny — w kolejności pierwszeństwa:

- Ustawienie `WEBSITES_WEB_CONTAINER_NAME` aplikacji ustawione na nazwę kontenera
- Pierwszy kontener definiujący port 80 lub 8080
- Jeśli żadna z powyższych jest true, pierwszy kontener zdefiniowany w pliku będą dostępne (narażone)


## <a name="web-sockets"></a>Gniazda internetowe

Gniazda sieci Web są obsługiwane w aplikacjach systemu Linux.

> [!IMPORTANT]
> Gniazda sieci Web nie są obecnie obsługiwane dla aplikacji systemu Linux w bezpłatnych planach obsługi aplikacji. Pracujemy nad usunięciem tego ograniczenia i planujemy obsługiwać do 5 połączeń z gniazdami internetowymi w bezpłatnych planach usługi app service.

## <a name="pricing-and-sla"></a>Ceny i umowy SLA

**Co to jest cennik, teraz, gdy usługa jest ogólnie dostępna?**

Ceny różnią się w zależności od jednostki SKU i regionu, ale więcej szczegółów można zobaczyć na naszej stronie cenowej: [Ceny usługi app service](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Inne pytania

**Co oznacza "Żądana funkcja nie jest dostępna w grupie zasobów"?**

Ten komunikat może zostać wyświetlony podczas tworzenia aplikacji sieci web przy użyciu usługi Azure Resource Manager (ARM). Na podstawie bieżącego ograniczenia dla tej samej grupy zasobów nie można mieszać aplikacji systemu Windows i Linux w tym samym regionie.

**Jakie są obsługiwane znaki w nazwach ustawień aplikacji?**

W ustawieniach aplikacji można używać tylko liter (A-Z, a-z), cyfr (0-9) i znaku podkreślenia (_).

**Gdzie mogę poprosić o nowe funkcje?**

Możesz przesłać swój pomysł na [forum opinii aplikacji Web Apps](https://aka.ms/webapps-uservoice). Dodaj "[Linux]" do tytułu swojego pomysłu.

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure App Service w systemie Linux?](app-service-linux-intro.md)
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ciągłe wdrażanie za pomocą aplikacji sieci Web dla kontenerów](./app-service-linux-ci-cd.md)
