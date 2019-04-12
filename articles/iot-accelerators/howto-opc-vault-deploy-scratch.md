---
title: Wdrażanie modułu magazynu OPC od podstaw — Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć magazyn OPC od podstaw.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496900"
---
# <a name="deploy-opc-vault-from-scratch"></a>Wdrażanie magazynu OPC od podstaw

OPC Vault to mikrousług, konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatu w przypadku serwera OPC UA i aplikacje klienckie w chmurze. W tym artykule pokazano, jak wdrożyć magazyn OPC od podstaw.

## <a name="configuration-and-environment-variables"></a>Zmienne konfiguracji i środowiska

Konfiguracja usługi są przechowywane przy użyciu platformy ASP.NET Core konfiguracji kart w appsettings.ini. Format pliku INI pozwala przechowywać wartości w czytelnym formacie z komentarzami.
Aplikacja obsługuje także wstawianie zmiennych środowiskowych, takich jak poświadczenia i szczegóły sieci. (W tej sekcji pierwotnie nosi TODO konfiguracji i zmiennymi środowiskowymi).

Plik konfiguracyjny w repozytorium odwołuje się do niektórych zmiennych środowiskowych, które należy utworzyć co najmniej raz. W zależności od Twojego systemu operacyjnego i środowiska IDE istnieje kilka sposobów zarządzania zmienne środowiskowe:

- Dla użytkowników Windows env var — plik setup.cmd skrypt musi zostać przygotowany i wykonywane tylko raz. Po wykonaniu ustawienia zostanie utrzymany między sesjami terminala i ponownego uruchamiania.

- W przypadku środowisk systemów Linux i OS x skrypt env var setup musi być wykonywane za każdym razem, gdy jest otwarty w nowej konsoli. W zależności od systemu operacyjnego i terminal istnieją sposoby utrwalania wartości globalnie, aby uzyskać więcej informacji, które powinny pomóc w tych stron:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Zmienne środowiskowe można również ustawić w programie Visual Studio, we właściwościach projektu, w okienku po lewej stronie wybierz pozycję "Właściwości konfiguracji" i "Environment", aby przejść do sekcji, w którym można dodać wiele zmiennych.

- Kierowcy IntelliJ: Zmienne środowiskowe można ustawić w każdej konfiguracji uruchamiania, podobnie jak IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Uruchamianie i debugowanie w programie Visual Studio

Program Visual Studio pozwala szybko otworzyć aplikację bez przy użyciu wiersza polecenia, bez konieczności konfigurowania niczego poza IDE.

Kroki przy użyciu programu Visual Studio 2017:

1. Otwórz rozwiązanie za pomocą `iot-opc-gds-service.sln` pliku.

1. Po załadowaniu rozwiązania, kliknij prawym przyciskiem myszy `WebService` projektu, wybierz, a następnie przejdź do `Debug` sekcji.

1. W tej samej sekcji zdefiniować zmienne środowiskowe wymagane.

1. Naciśnij klawisz **F5**, lub **Uruchom** ikony. Program Visual Studio powinna zostać otwarta przeglądarka przedstawiająca stan usługi w formacie JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Uruchamianie i debugowanie z kierowcą IntelliJ

1. Otwórz rozwiązanie za pomocą `iot-opc-gds-service.sln` pliku.

1. Po załadowaniu rozwiązania, przejdź do `Run > Edit Configurations` i Utwórz nowy `.NET Project` konfiguracji.

1. W konfiguracji wybierz projekt usługi sieci Web.

1. Zapisz ustawienia i Uruchom konfigurację, która jest tworzona na podstawie narzędzi IDE.

1. Usługa ładowania początkowego komunikaty w oknie Uruchamianie IntelliJ szczegółowe informacje, takie jak adres URL której działa usługa sieci web oraz usługi powinien zostać wyświetlony dzienniki.

## <a name="build-and-run-from-the-command-line"></a>Skompilować i uruchomić z wiersza polecenia

Folder skryptów zawiera niektóre skrypty dla częstych zadań:

- `build`: Kompiluj wszystkie projekty i uruchomić testy.

- `compile`: Kompiluj wszystkie projekty.

- `run`: Kompilowanie projektów, a następnie uruchom usługę, która wyświetla monit o podwyższonym poziomem uprawnień w Windows, aby uruchomić usługę sieci web.

Skrypty Sprawdź, czy Instalator zmiennych środowiskowych. Możesz ustawić zmienne środowiskowe globalnie w systemie operacyjnym lub użyj skryptu "env var setup" w folderze skryptów.

### <a name="sandbox"></a>Piaskownica

Skrypty przyjęto założenie, skonfigurować swoje Środowisko deweloperskie za pomocą platformy .NET Core i Docker. Można uniknąć instalacji platformy .NET Core i zainstalować tylko Docker i użyć parametru wiersza polecenia `--in-sandbox` (lub krótkiej formy `-s`), na przykład:

- `build --in-sandbox`: Wykonuje zadanie kompilacji w kontenerze platformy Docker (skrócona forma `build -s`).

- `compile --in-sandbox`: Wykonuje zadania kompilacji w kontenerze platformy Docker (skrócona forma `compile -s`).

- `run --in-sandbox`: Uruchamia usługę w kontenerze platformy Docker (skrócona forma `run -s`).

Obrazy platformy Docker używana dla obszaru piaskownicy znajdują się w usłudze Docker Hub [tutaj](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Pakiet aplikacji do obrazu platformy Docker

`scripts` Folder zawiera podfolder platformy docker z plików wymaganych do tworzenia pakietów usługi w obrazu platformy Docker:

- `Dockerfile`: Specyfikacje obrazów platformy docker.
- `build`: Tworzenie kontenera platformy Docker i przechowywania obrazu w rejestrze lokalnym.
- `run`: Uruchom kontener platformy Docker z obrazów przechowywanych w rejestrze lokalnym.
- `content`: Folder z plikami skopiowane do obrazu, w tym skrypt punktu wejścia.

## <a name="azure-iot-hub-setup"></a>Konfiguracja usługi Azure IoT Hub

Aby korzystać z mikrousług, należy skonfigurować usługi Azure IoT Hub dla programowania i integracji testów.

Projekt zawiera niektóre skrypty powłoki Bash, aby pomóc użytkownikowi w tej konfiguracji:

- Utwórz nowe Centrum IoT Hub: `./scripts/iothub/create-hub.sh`

- Wyświetl istniejące centra: `./scripts/iothub/list-hubs.sh`

- Szczegóły usługi IoT Hub (na przykład klucze): `./scripts/iothub/show-hub.sh`

I w przypadku, gdy masz wiele subskrypcji platformy Azure:

- Pokaż listę subskrypcji: `./scripts/iothub/list-subscriptions.sh`

- Zmiana bieżącej subskrypcji: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Tworzenie Instalatora

### <a name="net-setup"></a>Instalator platformy .NET

Przepływ pracy projektu jest zarządzana za pośrednictwem [platformy .NET Core](https://dotnet.github.io) 1.x, który należy zainstalować w danym środowisku, dzięki czemu mogą uruchamiać wszystkie skrypty i upewnij się, że środowisko IDE działa zgodnie z oczekiwaniami.

Firma Microsoft udostępnia również [wersji środowiska Java](https://github.com/Azure/iot-opc-gds-service-dotnet) tego projektu i inne składniki komputerów z systemem Azure IoT.

### <a name="ide"></a>IDE

Oto niektóre środowiska IDE, którego można użyć do pracy na komputerach z systemem Azure IoT:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio dla komputerów Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [Kierowcy IntelliJ](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Instalator usługi Git

Projekt zawiera Git hook do automatyzacji niektórych testów przed zaakceptowaniem zmiany w kodzie. Można ręcznie uruchomić testy, lub pozwolić platformy ciągłej integracji, aby uruchomić testy. Stosujemy następujące elementu Git hook automatyczne uruchamianie wszystkich testów przed wysłaniem zmian w kodzie w usłudze GitHub i przyspieszyć przepływ pracy tworzenia oprogramowania.

Jeśli w dowolnym momencie chcesz usunąć punkt zaczepienia, po prostu usunąć plik zainstalowane w ramach `.git/hooks`. Można również pominąć przy użyciu wstępnego zatwierdzenia punktu zaczepienia `--no-verify` opcji.

#### <a name="pre-commit-hook-with-sandbox"></a>Hook wstępnego zatwierdzenia z piaskownicą

Aby skonfigurować uwzględnione punkty zaczepienia, otwórz konsolę Windows/Linux/MacOS, a następnie wykonać:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

W przypadku tej konfiguracji podczas ewidencjonowania plików Git sprawdza, czy aplikacja pomyślnie przejdzie wszystkie testy uruchamiania kompilacji i testów w kontenerze platformy Docker, skonfigurowanego ze wszystkimi wymaganiami rozwoju.

#### <a name="pre-commit-hook-without-sandbox"></a>Hook wstępnego zatwierdzenia bez piaskownicy

> [!NOTE] 
> Wymaga punktu zaczepienia bez piaskownicy [platformy .NET Core](https://dotnet.github.io) w systemie ścieżki.

Aby skonfigurować uwzględnione punkty zaczepienia, otwórz konsolę Windows/Linux/MacOS, a następnie wykonać:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
W przypadku tej konfiguracji podczas ewidencjonowania plików Git sprawdza, czy aplikacja pomyślnie przejdzie wszystkie testy uruchamiania kompilacji i testów w swojej stacji roboczej za pomocą narzędzi zainstalowanych w systemie operacyjnym.

Wskazówki dotyczące stylu kodu projektu:

- W przypadku gdy jest to uzasadnione, wiersze długość jest ograniczona do 80 znaków max ułatwiające przeglądy kodu i edytory wiersza polecenia.

- Wcięcie bloków kodu z czterech spacji. Należy unikać char kartę.

- Pliki tekstowe, użyj Unix koniec linii (LF).

- Wstrzykiwanie zależności odbywa się za pomocą [Autofac](https://autofac.org).

- Pola interfejsów API usługi sieci Web są CamelCased, z wyjątkiem metadanych.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć magazyn OPC od podstaw, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Wdrażanie bliźniaczej reprezentacji OPC od podstaw](howto-opc-twin-deploy-modules.md)