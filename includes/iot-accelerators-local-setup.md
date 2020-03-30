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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183664"
---
## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Repozytoria kodu źródłowego zdalnego monitorowania zawierają kod źródłowy i pliki konfiguracyjne platformy Docker, które są potrzebne do uruchamiania obrazów platformy Docker mikrousług.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia, aby przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jeden z następujących zestawów poleceń, aby sklonować repozytorium .NET:

Aby pobrać najnowszą wersję implementacji mikrousług .NET, uruchom:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Te polecenia pobrać kod źródłowy dla wszystkich mikrousług oprócz skryptów używanych do uruchamiania mikrousług lokalnie. Chociaż nie potrzebujesz kodu źródłowego do uruchamiania mikrousług w docker, kod źródłowy jest przydatne, jeśli później planujesz zmodyfikować akcelerator rozwiązań i przetestować zmiany lokalnie.

## <a name="deploy-the-azure-services"></a>Wdrażanie usług platformy Azure

Chociaż w tym artykule pokazano, jak uruchomić mikrousług lokalnie, zależą one od usług platformy Azure uruchomionych w chmurze. Użyj następującego skryptu, aby wdrożyć usługi platformy Azure. Poniższe przykłady skryptów zakładają, że używasz repozytorium .NET na komputerze z systemem Windows. Jeśli pracujesz w innym środowisku, dostosuj odpowiednio ścieżki, rozszerzenia plików i separatory ścieżek.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli nie utworzono jeszcze wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia przejdź do folderu **\services\scripts\local\launch** w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować narzędzie **interfejsu wiersza** polecenia pcs i zalogować się na swoje konto platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom skrypt **start.cmd.** Skrypt monituje o następujące informacje:
   * Nazwa rozwiązania.
   * Subskrypcja platformy Azure, która ma być używana.
   * Lokalizacja centrum danych platformy Azure do użycia.

     Skrypt tworzy grupę zasobów na platformie Azure o nazwie rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure używane przez akcelerator rozwiązań. Tę grupę zasobów można usunąć, gdy nie są już potrzebne odpowiednie zasoby.

     Skrypt dodaje również zestaw zmiennych środowiskowych z prefiksem **PCS** do komputera lokalnego. Te zmienne środowiskowe zawierają szczegółowe informacje dotyczące zdalnego monitorowania, aby móc odczytywać z zasobu usługi Azure Key Vault. Ten zasób magazynu kluczy to miejsce, z którego zdalne monitorowanie odczyta wartości konfiguracji.

     > [!TIP]
     > Po zakończeniu skryptu zapisuje również zmienne środowiskowe w pliku o nazwie ** \<\>nazwa rozwiązania .env\>\\\\\<folderu macierzystego**. pcs . Można ich używać do przyszłych wdrożeń akceleratora rozwiązań. Należy zauważyć, że wszystkie zmienne środowiskowe ustawione na komputerze lokalnym zastępują wartości w **skryptach\\\\usług\\lokalnego** pliku env po uruchomieniu **docker-compose**.

1. Wyjdź ze środowiska wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Korzystanie z istniejących zasobów platformy Azure

Jeśli utworzono już wymagane zasoby platformy Azure, utwórz odpowiednie zmienne środowiskowe na komputerze lokalnym.
Ustaw zmienne środowiskowe dla następujących:
* **PCS_KEYVAULT_NAME** — nazwa zasobu usługi Azure Key Vault
* **PCS_AAD_APPID** - Identyfikator aplikacji AAD
* **PCS_AAD_APPSECRET** - Tajna aplikacja AAD

Wartości konfiguracji będą odczytywane z tego zasobu usługi Azure Key Vault. Te zmienne środowiskowe mogą być zapisywane w ** \<\>\\pliku\\\<\>.env folderu macierzystego** z wdrożenia. Należy zauważyć, że zmienne środowiskowe ustawione na komputerze lokalnym zastępują wartości w **skryptach\\\\usług\\lokalnego** pliku env po uruchomieniu **docker-compose**.

Niektóre konfiguracje wymagane przez mikrousługi są przechowywane w wystąpieniu **usługi Key Vault,** który został utworzony podczas początkowego wdrażania. Odpowiednie zmienne w keyvault powinny być modyfikowane w razie potrzeby.