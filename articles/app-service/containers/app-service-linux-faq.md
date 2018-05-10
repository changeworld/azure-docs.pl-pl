---
title: Usługa aplikacji Azure w systemie Linux — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa aplikacji Azure w systemie Linux — często zadawane pytania.
keywords: Usługa aplikacji Azure, aplikacji sieci web, często zadawane pytania, linux, oss
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
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 8d25c70a0e5db92bca6f3970049a2e1325fe124b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-on-linux-faq"></a>Usługa aplikacji Azure w systemie Linux — często zadawane pytania

Wraz z wydaniem usługi App Service w systemie Linux pracujemy nad Dodawanie funkcji i wprowadzać ulepszenia platformy. Ten artykuł zawiera odpowiedzi na pytania, że naszym klientom ma zostały prośbą ostatnio.

Jeśli masz pytania, komentarza, w artykule i firma Microsoft będzie odpowiedź tak szybko, jak to możliwe.

## <a name="built-in-images"></a>Wbudowane obrazów

**Chcę rozwidlania wbudowanych kontenerów Docker, które zapewnia platformę. Gdzie można znaleźć te pliki?**

Wszystkie pliki Docker można znaleźć w [GitHub](https://github.com/azure-app-service). Możesz znaleźć wszystkie kontenery Docker na [Centrum Docker](https://hub.docker.com/u/appsvc/).

**Co to są oczekiwanych wartości dla sekcji pliku startowego podczas konfigurowania środowiska uruchomieniowego stosu?**

Dla środowiska Node.js Określ plik konfiguracyjny PM2 lub plik skryptu. Określ nazwę skompilowanej biblioteki DLL dla platformy .NET Core. Dla środowiska Ruby można określić skrypt dopisków fonetycznych, który chcesz zainicjować aplikacji za pomocą.

## <a name="management"></a>Zarządzanie

**Co się stanie po naciśnięciu przycisku ponownego uruchomienia w portalu Azure?**

Ta akcja jest taka sama jak Docker ponownego uruchomienia komputera.

**Do nawiązania połączenia aplikacji kontenera maszyny wirtualnej (VM) można używać protokołu Secure Shell (SSH)?**

Tak, możesz to zrobić za pośrednictwem witryny zarządzania (SCM) kontroli źródła.

> [!NOTE] 
> Można również nawiązać kontenera aplikacji bezpośrednio z komputera lokalnego Programowanie przy użyciu protokołu SSH, SFTP lub Visual Studio Code (dla aplikacji Node.js debugowania na żywo). Aby uzyskać więcej informacji, zobacz [zdalnego debugowania i SSH w usłudze App Service w systemie Linux](https://aka.ms/linux-debug).
>

**Jak utworzyć planu usługi aplikacji Linux za pomocą zestawu SDK lub szablonu usługi Azure Resource Manager?**

Należy ustawić **zastrzeżone** pole app service w celu *true*.

## <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

**Moja aplikacja sieci web nadal używa stary obraz kontenera Docker po po aktualizacji obraz Centrum Docker. Są obsługiwane ciągłej integracji i wdrażanie niestandardowych kontenery?**

Aby skonfigurować ciągłej integracji/wdrożenia do rejestru kontenera platformy Azure lub DockerHub obrazy sprawdzając następujący artykuł [ciągłego wdrażania aplikacji sieci Web dla kontenerów](./app-service-linux-ci-cd.md). Dla prywatnych rejestrów można odświeżyć kontenera przez zatrzymanie i uruchomienie następnie aplikacji sieci web. Lub możesz zmienić lub Dodaj ustawienie aplikacji fikcyjny, aby wymusić odświeżenie z kontenera.

**Czy obsługują środowisk przemieszczania?**

Tak.

**Można użyć *narzędzia web deploy* wdrażania Moja aplikacja sieci web?**

Tak, należy określić aplikację nosi nazwę `WEBSITE_WEBDEPLOY_USE_SCM` do *false*.

**Wdrażanie Git Moja aplikacja zakończy się niepowodzeniem podczas korzystania z aplikacji sieci web systemu Linux. Jak mogę obejścia tego problemu?**

W przypadku niepowodzenia wdrożenia usługi Git do aplikacji sieci web systemu Linux można wybrać następujące opcje alternatywny, aby wdrożyć kod aplikacji:

- Użyj funkcji ciągłego dostarczania (wersja zapoznawcza): w repozytorium Git programu Team Services lub repozytorium GitHub, aby użyć ciągłego dostarczania Azure można przechowywać kodu źródłowego aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego dostarczania dla aplikacji sieci web Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Użyj [ZIP wdrażania interfejsu API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Aby użyć tego interfejsu API [SSH do aplikacji sieci web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) i przejdź do folderu, w której chcesz wdrożyć kod. Uruchom następujące polecenie:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Jeśli występuje błąd `curl` polecenie nie zostało znalezione, upewnij się, zainstaluj narzędzie curl przy użyciu `apt-get install curl` przed uruchomieniem poprzedniej `curl` polecenia.

## <a name="language-support"></a>Obsługa języków

**Chcę użyć websocket we własnej aplikacji Node.js ani specjalne ustawienia konfiguracji, aby ustawić?**

Tak, wyłącz `perMessageDeflate` w kodzie Node.js po stronie serwera. Na przykład jeśli używasz użyciu biblioteki socket.io, wykonaj następujące czynności:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Czy nie skompilowanego aplikacji .NET Core są obsługiwane?**

Tak.

**Są obsługiwane Composer Menedżer zależności dla aplikacji PHP?**

Tak. Podczas wdrażania narzędzia Git Kudu powinna wykryć wdrażana jest aplikacja PHP (dzięki użyciu obecność pliku composer.lock), czy aparat Kudu następnie wyzwoli instalacji composer dla Ciebie.

## <a name="custom-containers"></a>Kontenery niestandardowych

**Korzystam z własnych niestandardowych kontenera. Chcę platformy w celu zainstalowania udziału SMB do `/home/` katalogu.**

Możesz to zrobić przez ustawienie `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienia aplikacji na *true* lub przez usunięcie aplikacji ustawienie całkowicie. Należy pamiętać, że w ten sposób spowoduje ponowne uruchomienie kontenera, gdy magazynu platformy przechodzi przez zmianę. 

>[!NOTE]
>Jeśli `WEBSITES_ENABLE_APP_SERVICE_STORAGE` jest ustawienie *false*, `/home/` katalogu nie będą udostępniane między wystąpieniami skali i pliki, które są zapisywane nie zostaną utrwalone uruchomieniach.

**Moje niestandardowe kontenera zajmuje dużo czasu do uruchomienia, i platformy ponownego uruchomienia kontenera, przed zakończeniem uruchamiania.**

Możesz skonfigurować czas oczekiwania platformę przed jej ponownym uruchomieniu programu kontenera. Aby to zrobić, ustaw `WEBSITES_CONTAINER_START_TIME_LIMIT` ma wartość ustawienia aplikacji. Wartość domyślna to 230 sekund, a wartość maksymalna wynosi 600 sekund.

**Co to jest format adresu URL serwera prywatnej rejestru?**

Podaj adres URL rejestru pełnego tym `http://` lub `https://`.

**Co to jest format nazwy obrazu w opcji prywatnych rejestru?**

Dodaj nazwę pełnego obrazu, łącznie z rejestru prywatny adres URL (na przykład myacr.azurecr.io/dotnet:latest). Nazwy, które użyć portu niestandardowego obrazu [nie można wprowadzić za pośrednictwem portalu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Aby ustawić `docker-custom-image-name`, użyj [ `az` narzędzia wiersza polecenia](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Moje obrazu niestandardowego kontenera mogą uwidaczniać więcej niż jeden port?**

Firma Microsoft nie obsługują aktualnie udostępnianie więcej niż jeden port.

**Czy można przełączyć własny magazyn?**

Firma Microsoft nie obsługują aktualnie dostarczają własne magazynu.

**Dlaczego nie można przeglądać Moje niestandardowe kontenera procesy systemu lub z systemem plików z witryny SCM?**

Witryna SCM jest uruchamiana w oddzielnych kontenera. Nie można sprawdzić procesy systemu lub z systemem plików kontenera aplikacji.

**Moje niestandardowe kontenera nasłuchuje portu innego niż port 80. Jak można skonfigurować mojej aplikacji można przekierować żądania do tego portu**

Mamy automatyczne wykrywanie portu. Można również określić, że aplikacja nosi nazwę *WEBSITES_PORT* i nadaj mu wartość numeru portu oczekiwanego. Wcześniej, platforma używane *portu* ustawienia aplikacji. Firma Microsoft planuje można zastąpić to ustawienie aplikacji i używania *WEBSITES_PORT* wyłącznie.

**Należy do implementacji protokołu HTTPS w mojej niestandardowych kontenera?**

Nie, platforma obsługuje zakończenia połączenia HTTPS na końcach front udostępnionego.

## <a name="pricing-and-sla"></a>Cennik i umowy SLA

**Co to jest cennika, skoro usługa jest ogólnie dostępna?**

Naliczane są opłaty normalne cennik usługi aplikacji Azure przez liczbę godzin, w których aplikacja będzie działać.

## <a name="other-questions"></a>Inne pytania

**Co to są obsługiwane znaki w nazwach ustawienia aplikacji?**

Dla ustawienia aplikacji, można użyć tylko litery (A-Z, a – z), cyfry (0-9) i znak podkreślenia (_).

**Gdzie mogą żądać nowych funkcji**

Możesz podać Twój pomysł na [forum opinii aplikacje sieci Web](https://aka.ms/webapps-uservoice). Do tytułu Twój pomysł, należy dodać "[Linux]".

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Azure App Service w systemie Linux?](app-service-linux-intro.md)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie w aplikacji sieci Web dla kontenerów](./app-service-linux-ci-cd.md)
