---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 10/29/2018
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 900d75f826830ea7336044a892506d3bec546e30
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283934"
---
## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Zdalne monitorowanie repozytoriów kodu źródłowego obejmują pliki konfiguracji platformy Docker, należy uruchomić mikrousługi obrazów platformy Docker.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia można przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jedno z następujących zestawów poleceń, aby sklonować albo repozytorium .NET lub Java:

Aby pobrać najnowszą wersję implementacji mikrousługi platformy .NET, uruchom polecenie:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

Aby pobrać najnowszą wersję implementacji mikrousług Java, uruchom polecenie:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Te polecenia Pobierz kod źródłowy wszystkie mikrousługi oprócz skryptów, których można używać do uruchamiania mikrousługi lokalnie. Mimo że nie ma potrzeby kodu źródłowego, aby uruchomić mikrousług na platformie Docker, kod źródłowy jest przydatne, jeśli planujesz później zmodyfikować akcelerator rozwiązań i lokalne testowanie zmian.

## <a name="deploy-the-azure-services"></a>Wdrażaj usługi platformy Azure

Mimo że w tym artykule pokazano, jak uruchomić mikrousługi lokalnie, są one zależne od usług platformy Azure działające w chmurze. Użyj poniższego skryptu wdrażania usług platformy Azure. W poniższych przykładach skryptów przyjęto założenie, że używasz repozytorium .NET na maszynie Windows. Jeśli pracujesz w innym środowisku, Dostosuj ścieżek, rozszerzeń plików i separatorami ścieżki odpowiednio.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli jeszcze nie utworzono wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia i przejdź do **\services\scripts\local\launch** folderu w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować **komputerów** interfejsu wiersza polecenia narzędzia i zaloguj się do konta platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom **start.cmd** skryptu. Skrypt wyświetli monit o podanie następujących informacji:
    * Nazwa rozwiązania.
    * Subskrypcja platformy Azure, która ma być używana.
    * Lokalizacja centrum danych platformy Azure do użycia.

    Skrypt tworzy grupę zasobów na platformie Azure, nazwą rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure, który korzysta z akceleratora rozwiązań. Można usunąć tej grupy zasobów, gdy nie są już potrzebne odpowiednich zasobów.

    Skrypt ten dodaje również zestaw zmiennych środowiskowych z prefiksem **komputerów** na komputer lokalny. Uruchamianie kontenerów platformy Docker lokalnie będzie odczytać wartości konfiguracji z tych zmiennych środowiskowych.

> [!TIP]
> Po ukończeniu działania skryptu, wyświetla listę zmiennych środowiskowych. Po zapisaniu tych wartości, aby **usług\\skrypty\\lokalnego\\ENV** pliku umożliwia ich wdrożenia akcelerator rozwiązań w przyszłości. Należy pamiętać, że wszelkie zmienne środowiskowe, ustaw na komputerze lokalnym zastąpić wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **docker-compose**.

### <a name="use-existing-azure-resources"></a>Użyj istniejących zasobów platformy Azure

Jeśli już utworzono wymaganych zasobów platformy Azure, utwórz odpowiednie zmienne środowiskowe na komputerze lokalnym. Zapisano te wartości w **usług\\skrypty\\lokalnego\\ENV** pliku w ramach ostatniego wdrożenia. Należy pamiętać, że zmienne środowiskowe ustawione na komputerze lokalnym, Zastąp wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **narzędzia docker compose**.