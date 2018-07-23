---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie — Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak wdrożyć zdalnego monitorowania akcelerator rozwiązań do maszyny lokalnej na potrzeby projektowania i testowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188792"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. To podejście mikrousługi są wdrażane na lokalny kontener platformy Docker i korzysta z usługi IoT Hub, Cosmos DB i usług Azure storage w chmurze. Możesz użyć akceleratorów rozwiązań (komputery) interfejsu wiersza polecenia do wdrożenia usługi Azure cloud services.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Narzędzia docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia.
* KOMPUTERY INTERFEJSU WIERSZA POLECENIA
* Lokalne repozytorium kodu źródłowego

> [!NOTE]
> Te narzędzia są dostępne na wielu platformach, w tym Windows i Linux oraz dla systemu iOS.

### <a name="install-the-pcs-cli"></a>Zainstaluj interfejs wiersza polecenia komputerów

Aby zainstalować interfejs wiersza polecenia komputerów za pomocą Menedżera npm, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
npm install iot-solutions -g
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [sposób używania interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Pobierz kod źródłowy

 Monitorowanie zdalne repozytorium kodu źródłowego zawiera pliki konfiguracji platformy Docker, których należy pobrać, konfigurowanie i uruchamianie obrazów platformy Docker, które zawierają mikrousług. Aby sklonować i utworzyć lokalną wersję repozytorium, przejdź do odpowiedniego folderu na komputerze lokalnym przy użyciu Twojego ulubionego wiersza polecenia lub terminalu i uruchom jedno z następujących poleceń:

Aby zainstalować implementacji języka Java, mikrousługi, uruchom polecenie:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Aby zainstalować implementacje platformy .net, mikrousługi, uruchom polecenie:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Zdalne repozytorium Monioring we wstępnie skonfigurowanym rozwiązaniu & moduły podrzędne [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Te polecenia Pobierz kod źródłowy wszystkie mikrousługi. Mimo że nie ma potrzeby kodu źródłowego, aby uruchomić mikrousług na platformie Docker, kod źródłowy jest przydatne, jeśli planujesz później zmodyfikować wstępnie skonfigurowanego rozwiązania i lokalne testowanie zmian.

## <a name="deploy-the-azure-services"></a>Wdrażaj usługi platformy Azure

Mimo że w tym artykule pokazano, jak uruchomić mikrousługi lokalnie, są one zależne od trzech usług platformy Azure działające w chmurze. Można wdrożyć te usługi platformy Azure [ręcznie za pomocą witryny Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), lub za pomocą interfejsu wiersza polecenia komputerów. W tym artykule dowiesz się, jak używać `pcs` narzędzia.

### <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Zanim będzie można wdrożyć akcelerator rozwiązań, musisz zarejestrować się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
pcs login
```

Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć procesu logowania. Upewnij się, że użytkownik nie kliknij w dowolnym miejscu wewnętrzne interfejsu wiersza polecenia lub logowanie może zakończyć się niepowodzeniem. Po zakończeniu logowania, zostanie wyświetlony komunikat pomyślne logowanie w interfejsie wiersza polecenia. 

### <a name="run-a-local-deployment"></a>Uruchom wdrożenie lokalne

Użyj następującego polecenia, aby uruchomić wdrożenie lokalne. Spowoduje to utworzenie wymaganych zasobów platformy azure i wydrukuj zmienne środowiskowe do konsoli. 

```cmd/pcs
pcs -s local
```

Skrypt wyświetli monit o podanie następujących informacji:

* Nazwa rozwiązania.
* Subskrypcja platformy Azure, która ma być używana.
* Lokalizacja centrum danych platformy Azure do użycia.

> [!NOTE]
> Skrypt utworzy Centrum IoT Hub wystąpienie, wystąpienie usługi Cosmos DB i konto magazynu platformy Azure w grupie zasobów w subskrypcji platformy Azure. Nazwa grupy zasobów jest nazwą rozwiązania wybrano podczas uruchomienia `pcs` narzędzie powyżej. 

> [!IMPORTANT]
> Skrypt zajmuje kilka minut. Po zakończeniu zostanie wyświetlony komunikat `Copy the following environment variables to /scripts/local/.env file:`. Kopiowanie szczegółów środowiska definicje zmiennych po wiadomości, użyjesz ich w późniejszym kroku.

## <a name="run-the-microservices-in-docker"></a>Uruchom mikrousług na platformie Docker

Aby uruchomić mikrousług na platformie Docker, należy najpierw edytować **skrypty\\lokalnego\\ENV** pliku w swojej lokalnej kopii repozytorium sklonowane w poprzednim kroku powyżej. Zamień całą zawartość pliku z definicjami zmiennych środowiska, należy zanotować po uruchomieniu `pcs` polecenia w ostatnim kroku. Te zmienne środowiskowe Włącz mikrousług w kontenerze platformy Docker, aby nawiązać połączenie z usługami platformy Azure, które zostały utworzone przez `pcs` narzędzia.

Aby uruchomić akcelerator rozwiązań, przejdź do **scripts\local** folder w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd\sh
docker-compose up
```

Podczas pierwszego uruchamiania tego polecenia Docker pobiera obrazy mikrousług z usługi Docker hub do tworzenia kontenerów lokalnie. W kolejnych uruchomień Docker uruchamia kontenery natychmiast.

Aby wyświetlić dzienniki z kontenera, można użyć oddzielnych powłoki. Najpierw Znajdź przy użyciu Identyfikatora kontenera `docker ps -a` polecenia. Następnie użyj `docker logs {container-id} --tail 1000` Aby wyświetlić ostatnie wpisy dziennika 1000 dla określonego kontenera.

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego, przejdź do [ http://localhost:8080 ](http://localhost:8080) w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania, należy usunąć usługi w chmurze z subskrypcji platformy Azure. Jest najprostszym sposobem usuwania usług, aby przejść do [witryny Azure portal](https://ms.portal.azure.com) i Usuń grupę zasobów utworzoną za pomocą `pcs` narzędzia.

Użyj `docker-compose down --rmi all` polecenie, aby usunąć obrazy platformy Docker i wolnego miejsca na komputerze lokalnym. Możesz także usunąć lokalną kopię repozytorium zdalne monitorowanie utworzone podczas klonowania kodu źródłowego z repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj lokalne Środowisko deweloperskie
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->