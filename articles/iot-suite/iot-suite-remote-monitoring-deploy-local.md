---
title: Wdrożenie zdalne monitorowania lokalnie - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób wdrażania zdalnego monitorowania akcelerator rozwiązań na komputerze lokalnym do testowania i tworzenia.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: a470987c4e8b5755554e4827cf1295a174d301e8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774479"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Wdrażanie zdalne monitorowania akcelerator rozwiązań lokalnie

W tym artykule przedstawiono sposób wdrażania zdalnego monitorowania akcelerator rozwiązań na komputerze lokalnym do testowania i tworzenia. Takie podejście wdraża mikrousług lokalnego kontenerze Docker i używa Centrum IoT, rozwiązania Cosmos bazę danych i usług Azure storage w chmurze. Możesz użyć akceleratorów rozwiązania (komputery) interfejsu wiersza polecenia do wdrożenia usługi w chmurze Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć używane przez zdalne akcelerator rozwiązań monitorowania usług Azure, musisz mieć aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

Do wykonania lokalnego wdrożenia, potrzebne są następujące narzędzia zainstalowane na komputerze deweloperskim lokalnego:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Rozwiązania docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z interfejsu wiersza polecenia.
* KOMPUTERY INTERFEJSU WIERSZA POLECENIA
* Lokalne repozytorium kodu źródłowego

> [!NOTE]
> Te narzędzia są dostępne na wiele platform, w tym Windows, Linux lub z systemem iOS.

### <a name="install-the-pcs-cli"></a>Zainstaluj interfejs wiersza polecenia komputerów

Aby zainstalować CLI komputerów za pomocą Menedżera npm, uruchom następujące polecenie w wierszu polecenia środowiska:

```cmd/sh
npm install iot-solutions -g
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [sposób użycia interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Pobieranie kodu źródłowego

 Zdalne repozytorium kodu źródłowego monitorowania obejmuje Docker pliki konfiguracji, które należy pobrać, skonfigurować i uruchomić obrazów Docker, które zawierają mikrousług. Klonowania i utworzyć lokalną wersję repozytorium, przejdź do odpowiedniego folderu na komputerze lokalnym za pomocą Twoje ulubione wiersza polecenia lub terminalu i uruchom jedno z następujących poleceń:

Aby zainstalować implementacje Java mikrousług, uruchom polecenie:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Aby zainstalować implementacje .net mikrousług, uruchom polecenie:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Zdalne repozytorium Monioring wstępnie skonfigurowane rozwiązanie & modułów podrzędnych [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Te polecenia Pobierz kod źródłowy dla wszystkich mikrousług. Mimo że nie ma potrzeby kodu źródłowego, aby uruchomić mikrousług w Docker, kod źródłowy jest przydatne, jeśli planujesz później zmodyfikować wstępnie skonfigurowane rozwiązanie i przetestuj zmiany lokalnie.

## <a name="deploy-the-azure-services"></a>Wdrażanie usług Azure

Mimo że w tym artykule pokazano, jak uruchomić mikrousług lokalnie, zależą one od trzech usług platformy Azure w chmurze. Można wdrożyć tych usług Azure [ręcznie za pośrednictwem portalu Azure](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), lub użyj interfejsu wiersza polecenia komputerów. W tym artykule przedstawiono sposób użycia `pcs` narzędzia.

### <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Przed wdrożeniem akcelerator rozwiązań, musisz się zalogować do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
pcs login
```

Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć procesu logowania. Upewnij się, że użytkownik nie kliknij w dowolnym miejscu wewnątrz interfejsu wiersza polecenia lub nazwy logowania nie powiedzie. Zostanie wyświetlony komunikat pomyślnego logowania w interfejsu wiersza polecenia, po zakończeniu logowania. 

### <a name="run-a-local-deployment"></a>Uruchamianie lokalnego wdrożenia

Użyj następującego polecenia, aby uruchomić wdrożenia lokalnego. Spowoduje to utworzenie wymaganych zasobów platformy azure i wydrukuj zmienne environemnt do konsoli. 

```cmd/pcs
pcs -s local
```

Skrypt wyświetla monit o podanie poniższych informacji:

* Nazwa rozwiązania.
* Subskrypcja platformy Azure, która ma być używana.
* Lokalizacja centrum danych Azure do użycia.

> [!NOTE]
> Skrypt tworzy Centrum IoT wystąpienia, wystąpienie rozwiązania Cosmos bazy danych i konto magazynu platformy Azure w grupie zasobów w Twojej subskrypcji platformy Azure. Nazwa grupy zasobów jest nazwą rozwiązania wybrano podczas uruchomienia `pcs` narzędzie powyżej. 

> [!IMPORTANT]
> Skrypt może zająć kilka minut do uruchomienia. Po zakończeniu instalacji zostanie wyświetlony komunikat z `Copy the following environment variables to /scripts/local/.env file:`. Kopiowanie środowiska w dół po wiadomości, definicje zmiennych użyjesz ich w kolejnym kroku.

## <a name="run-the-microservices-in-docker"></a>Uruchom mikrousług w Docker

Aby uruchomić mikrousług Docker, należy najpierw zmodyfikować **skryptów\\lokalnego\\.env** pliku w lokalnej kopii repozytorium sklonować we wcześniejszym kroku powyżej. Zamień całą zawartość pliku definicje zmiennych środowiska należy zanotować za `pcs` polecenia w ostatnim kroku. Te zmienne środowiskowe umożliwić mikrousług w kontenerze Docker do łączenia się z usługami Azure utworzonych przez `pcs` narzędzia.

Aby uruchomić akcelerator rozwiązań, przejdź do **scripts\local** folderu w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd\sh
docker-compose up
```

Przy pierwszym uruchomieniu tego polecenia Docker pobiera obrazy mikrousługi z Centrum Docker tworzenie kontenerów lokalnie. W kolejnych uruchomieniach Docker uruchamia natychmiast kontenerów.

Oddzielne powłoki służy do wyświetlania dzienników z kontenera. Najpierw znaleźć przy użyciu Identyfikatora kontenera `docker ps -a` polecenia. Następnie użyj `docker logs {container-id} --tail 1000` do wyświetlania ostatnich wpisów dziennika 1000 określonego kontenera.

Aby uzyskać dostęp do zdalnego pulpitu nawigacyjnego monitorowania rozwiązania, przejdź do [ http://localhost:8080 ](http://localhost:8080) w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych kosztów, po zakończeniu testowania, należy usunąć usługi w chmurze z subskrypcją platformy Azure. Najprostszym sposobem usunięcia usług jest można przejść do [portalu Azure](https://ms.portal.azure.com) i usunąć grupy zasobów utworzonej za pomocą `pcs` narzędzia.

Użyj `docker-compose down --rmi all` polecenie, aby usunąć obrazy usługi Docker i wolnego miejsca na komputerze lokalnym. Możesz także usunąć lokalną kopię zdalnego repozytorium monitorowania utworzenia sklonowanego kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska programowania lokalnego
> * Skonfiguruj akcelerator rozwiązań
> * Wdrażanie akcelerator rozwiązań
> * Zaloguj się do akcelerator rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego następnym krokiem jest [Poznaj możliwości pulpit nawigacyjny rozwiązania](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->