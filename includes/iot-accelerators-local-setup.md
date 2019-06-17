---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111700"
---
## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Zdalne monitorowanie repozytoriów kodu źródłowego zawierają kod źródłowy i pliki konfiguracji platformy Docker, należy uruchomić mikrousługi obrazów platformy Docker.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia można przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jedno z następujących zestawów poleceń, aby sklonować albo repozytorium .NET:

Aby pobrać najnowszą wersję implementacji mikrousługi platformy .NET, uruchom polecenie:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
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

     Skrypt ten dodaje również zestaw zmiennych środowiskowych z prefiksem **komputerów** na komputer lokalny. Te zmienne środowiskowe zawierają szczegółowe informacje dotyczące monitorowania zdalnego był możliwy odczyt z zasobem usługi Azure Key Vault. Ten zasób usługi Key Vault jest, gdzie zdalne monitorowanie odczyta jego wartości konfiguracji z.

     > [!TIP]
     > Po ukończeniu działania skryptu, zapisuje również zmiennych środowiskowych w pliku o nazwie  **\<folderu macierzystego\>\\.pcs\\\<Nazwa rozwiązania\>ENV** . Można je dla wdrożenia akcelerator rozwiązań w przyszłości. Należy pamiętać, że wszelkie zmienne środowiskowe, ustaw na komputerze lokalnym zastąpić wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **docker-compose**.

1. Zamknij ze środowiska wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Użyj istniejących zasobów platformy Azure

Jeśli już utworzono wymaganych zasobów platformy Azure, utwórz odpowiednie zmienne środowiskowe na komputerze lokalnym.
Ustaw zmienne środowiskowe do wykonania poniższych czynności:
* **PCS_KEYVAULT_NAME** — Nazwa zasobu usługi Azure Key Vault
* **PCS_AAD_APPID** — identyfikator aplikacji usługi AAD
* **PCS_AAD_APPSECRET** — klucz tajny aplikacji usługi AAD

Wartości konfiguracji będą odczytywane z tego zasobu usługi Azure Key Vault. Te zmienne środowiskowe mogą być zapisane w  **\<folderu macierzystego\>\\.pcs\\\<Nazwa rozwiązania\>ENV** pliku z wdrożenia. Należy pamiętać, że zmienne środowiskowe ustawione na komputerze lokalnym, Zastąp wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **narzędzia docker compose**.

Niektórych elementów konfiguracji wymaganych przez mikrousługi są przechowywane w wystąpieniu **usługi Key Vault** utworzony na początkowym wdrożeniu. Odpowiednie zmienne w magazynie keyvault powinien być modyfikowany, zgodnie z potrzebami.