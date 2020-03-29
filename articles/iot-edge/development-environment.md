---
title: Środowisko programistyczne Usługi Azure IoT Edge | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsługiwanych systemach i zewnętrznych narzędziach programistycznych, które pomogą Ci tworzyć moduły IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511097"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Przygotowanie środowiska deweloperskiego i testowego dla aplikacji IoT Edge

Usługa Azure IoT Edge przenosi istniejącą logikę biznesową na urządzenia działające na urządzeniach brzegowych. Aby przygotować aplikacje i obciążenia do pracy jako [moduły IoT Edge,](iot-edge-modules.md)należy utworzyć je jako kontenery. Ten artykuł zawiera wskazówki dotyczące konfigurowania środowiska programistycznego, dzięki czemu można pomyślnie utworzyć rozwiązanie usługi IoT Edge. Po skonfigurowaniu środowiska programistycznego możesz dowiedzieć się, jak [tworzyć własne moduły IoT Edge.](module-development.md)

W każdym rozwiązaniu IoT Edge istnieją co najmniej dwie maszyny do rozważenia. Jednym z nich jest samo urządzenie (lub urządzenia) IoT Edge, które uruchamia moduł IoT Edge. Drugi to maszyna deweloperska, której używasz do tworzenia, testowania i wdrażania modułów. W tym artykule koncentruje się przede wszystkim na maszynie deweloperskiej. Do celów testowych obie maszyny mogą być takie same. IoT Edge można uruchomić na komputerze deweloperskim i wdrożyć moduły do niego.

## <a name="operating-system"></a>System operacyjny

Usługa Azure IoT Edge działa na określonym zestawie [obsługiwanych systemów operacyjnych.](support.md) Do tworzenia dla usługi IoT Edge, można użyć większości systemów operacyjnych, które można uruchomić aparat kontenera. Aparat kontenerów jest wymagane na komputerze deweloperskim do tworzenia modułów jako kontenerów i wypychania ich do rejestru kontenerów.

Jeśli na komputerze deweloperskim nie można uruchomić usługi Azure IoT Edge, przejdź do tego artykułu, aby dowiedzieć się więcej o [narzędziach testujących,](#testing-tools) które pomagają testować i debugować lokalnie.

System operacyjny komputera dewelopera nie musi być zgodny z systemem operacyjnym urządzenia IoT Edge. Jednak system operacyjny kontenera musi być spójny między komputerem deweloperskim a urządzeniem Usługi IoT Edge. Na przykład można tworzyć moduły na komputerze z systemem Windows i wdrażać je na urządzeniu z systemem Linux. Komputer z systemem Windows musi uruchomić kontenery Systemu Linux, aby zbudować moduły dla urządzenia z systemem Linux.

## <a name="container-engine"></a>Silnik kontenerowy

Główną koncepcją usługi IoT Edge jest możliwość zdalnego wdrażania logiki biznesowej i chmury na urządzeniach, pakując je do kontenerów. Do tworzenia kontenerów, potrzebujesz aparatu kontenera na komputerze deweloperskim.

Jedynym obsługiwanym aparatem kontenerów dla urządzeń Usługi IoT Edge w produkcji jest Moby. Jednak każdy aparat kontenera zgodny z Open Container Initiative, takich jak Docker, jest w stanie tworzenia obrazów modułów usługi IoT Edge.

## <a name="development-tools"></a>Narzędzia programistyczne

Zarówno Visual Studio, jak i Visual Studio Code mają rozszerzenia dodatków ułatwiające opracowywanie rozwiązań IoT Edge. Rozszerzenia te zapewniają szablony specyficzne dla języka, aby ułatwić tworzenie i wdrażanie nowych scenariuszy usługi IoT Edge. Rozszerzenia usługi Azure IoT Edge dla programu Visual Studio i visual studio kod ułatwia kod, tworzenie, wdrażanie i debugowanie rozwiązań usługi IoT Edge. Można utworzyć całe rozwiązanie usługi IoT Edge, które zawiera wiele modułów, a rozszerzenia automatycznie aktualizują szablon manifestu wdrożenia z każdym dodatkiem nowego modułu. Za pomocą rozszerzeń można również zarządzać urządzeniami IoT z poziomu programu Visual Studio lub kodu programu Visual Studio. Wdrażanie modułów na urządzeniu, monitorowanie stanu i wyświetlanie komunikatów po ich przybyciu do usługi IoT Hub. Oba rozszerzenia używają [narzędzia deweloperskiego IoT EdgeHub,](#iot-edgehub-dev-tool) aby włączyć lokalne uruchamianie i debugowanie modułów na komputerze deweloperskim.

Jeśli wolisz tworzyć z innymi edytorami lub z interfejsu wiersza polecenia, narzędzie deweloperskie usługi Azure IoT Edge udostępnia polecenia, dzięki czemu można tworzyć i testować z wiersza polecenia. Można tworzyć nowe scenariusze usługi IoT Edge, tworzyć obrazy modułów, uruchamiać moduły w symulatorze i monitorować wiadomości wysyłane do usługi IoT Hub.

### <a name="visual-studio-code-extension"></a>Rozszerzenie programu Visual Studio Code

Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code udostępnia szablony modułów usługi IoT Edge oparte na językach programowania, w tym C, C#, Java, Node.js i Python, a także funkcjach platformy Azure w języku C#.

Aby uzyskać więcej informacji i pobrać, zobacz [Narzędzia IoT platformy Azure dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Oprócz rozszerzeń usługi IoT Edge pomocne może okazać się zainstalowanie dodatkowych rozszerzeń do tworzenia. Na przykład można użyć [pomocy technicznej platformy Docker dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) do zarządzania obrazami, kontenerami i rejestrami. Ponadto wszystkie główne obsługiwane języki mają rozszerzenia dla programu Visual Studio Code, które mogą pomóc podczas tworzenia modułów.

#### <a name="prerequisites"></a>Wymagania wstępne

Szablony modułów dla niektórych języków i usług mają wymagania wstępne, które są niezbędne do tworzenia folderów projektu na komputerze deweloperskim za pomocą programu Visual Studio Code.

| Szablon modułu | Wymagania wstępne |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Zestaw rozwojowy Java SE 10](https://aka.ms/azure-jdks) <li> [Ustawianie zmiennej środowiskowej JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generator modułów usługi Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Rozszerzenie programu Visual Studio 2017/2019

Narzędzia usługi Azure IoT Edge dla programu Visual Studio zapewniają szablon modułu usługi IoT Edge utworzony w językach C# i C.

Aby uzyskać więcej informacji i pobrać, zobacz [Narzędzia usługi Azure IoT Edge tools dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) lub [Narzędzia usługi Azure IoT Edge tools dla programu Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Narzędzie deweloperskie IoT Edge

Narzędzie deweloperskie usługi Azure IoT Edge upraszcza tworzenie usługi IoT Edge za pomocą możliwości wiersza polecenia. To narzędzie udostępnia polecenia interfejsu wiersza polecenia do tworzenia, debugowania i testowania modułów. Narzędzie deweloperskie usługi IoT Edge współpracuje z systemem deweloperskim, niezależnie od tego, czy ręcznie zainstalowano zależności na komputerze, czy też używasz kontenera deweloperów usługi IoT Edge.

Aby uzyskać więcej informacji i rozpocząć pracę, zobacz [Wiki narzędzia deweloperskie IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Narzędzia do testowania

Istnieje kilka narzędzi testowych ułatwiających bardziej efektywne symulowanie urządzeń IoT Edge lub modułów debugowania. W poniższej tabeli przedstawiono porównanie wysokiego poziomu między narzędziami, a następnie poszczególne sekcje opisują każde narzędzie bardziej szczegółowo.

Tylko środowisko uruchomieniowe usługi IoT Edge jest obsługiwane w przypadku wdrożeń produkcyjnych, ale poniższe narzędzia umożliwiają symulowanie lub łatwe tworzenie urządzeń usługi IoT Edge do celów programowania i testowania. Narzędzia te nie wykluczają się wzajemnie, ale mogą współpracować w celu pełnego doświadczenia w rozwoju.

| Narzędzie | Znany również jako | Obsługiwane platformy | Najlepsze dla |
| ---- | ------------- | ------------------- | --------- |
| Narzędzie deweloperskie IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Symulowanie urządzenia do debugowania modułów. |
| Kontener deweloperski usługi IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Tworzenie bez instalowania zależności. |
| Środowisko uruchomieniowe usługi IoT Edge w kontenerze | iotedgec ( iotedgec ) | Windows, Linux, MacOS, ARM | Testowanie na urządzeniu, które może nie obsługiwać środowiska wykonawczego. |
| Kontener urządzenia IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testowanie scenariusza z wieloma urządzeniami IoT Edge na dużą skalę. |

### <a name="iot-edgehub-dev-tool"></a>Narzędzie deweloperskie IoT EdgeHub

Narzędzie deweloperskie usługi Azure IoT EdgeHub zapewnia lokalne środowisko programowania i debugowania. Narzędzie pomaga uruchamiać moduły IoT Edge bez środowiska wykonawczego usługi IoT Edge, dzięki czemu można tworzyć, rozwijać, testować, uruchamiać i debugować moduły i rozwiązania usługi IoT Edge lokalnie. Nie trzeba wypychać obrazów do rejestru kontenerów i wdrożyć je na urządzeniu do testowania.

Narzędzie deweloperskie IoT EdgeHub zostało zaprojektowane do pracy w połączeniu z rozszerzeniami programu Visual Studio i Visual Studio Code, a także z narzędziem deweloperskim usługi IoT Edge. Obsługuje tworzenie pętli wewnętrznej, a także testowanie pętli zewnętrznej, więc integruje się z narzędziami DevOps.

Aby uzyskać więcej informacji i zainstalować, zobacz [narzędzie deweloperskie usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Kontener deweloperski usługi IoT Edge

Kontener deweloperski usługi Azure IoT Edge to kontener platformy Docker, który ma wszystkie zależności potrzebne do tworzenia usługi IoT Edge. Ten kontener ułatwia rozpoczęcie pracy z dowolnym językiem, w którym chcesz się rozwijać, w tym c#, Python, Node.js i Java. Wszystko, co musisz zainstalować, to aparat kontenera, taki jak Docker lub Moby, aby wyciągnąć kontener do komputera deweloperającego.

Aby uzyskać więcej informacji, zobacz [kontener deweloperski usługi Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Środowisko uruchomieniowe usługi IoT Edge w kontenerze

Środowisko uruchomieniowe usługi IoT Edge w kontenerze zapewnia pełne środowisko wykonawcze, które przyjmuje parametry połączenia urządzenia jako zmienną środowiskową. Ten kontener umożliwia testowanie modułów i scenariuszy usługi IoT Edge w systemie, który może nie obsługiwać środowiska wykonawczego natywnie, takich jak MacOS. Wszystkie moduły, które można wdrożyć zostaną uruchomione poza kontenerem środowiska wykonawczego. Jeśli chcesz, aby środowisko wykonawcze i wszystkie wdrożone moduły istniały w tym samym kontenerze, należy wziąć pod uwagę kontener urządzenia usługi IoT Edge.

Aby uzyskać więcej informacji, zobacz [Uruchamianie usługi Azure IoT Edge w kontenerze](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Kontener urządzenia IoT Edge

Kontener urządzenia Usługi IoT Edge to kompletne urządzenie usługi IoT Edge, gotowe do uruchomienia na dowolnym komputerze z silnikiem kontenerowym. Kontener urządzenia zawiera środowisko uruchomieniowe usługi IoT Edge i sam aparat kontenera. Każde wystąpienie kontenera jest w pełni funkcjonalnym urządzeniem usługi IoT Edge inicjowania obsługi administracyjnej. Kontener urządzenia obsługuje zdalne debugowanie modułów, o ile istnieje droga sieciowa do modułu. Kontener urządzenia jest dobry do szybkiego tworzenia dużej liczby urządzeń usługi IoT Edge do testowania scenariuszy na dużą skalę lub potoków platformy Azure. Obsługuje również wdrażanie do kubernetes za pośrednictwem helm.

Aby uzyskać więcej informacji, zobacz [kontener urządzeń usługi Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Narzędzia DevOps

Gdy będziesz gotowy do opracowania rozwiązań na dużą skalę dla rozbudowanych scenariuszy produkcyjnych, skorzystaj z nowoczesnych zasad DevOps, w tym automatyzacji, monitorowania i usprawnionych procesów inżynierii oprogramowania. Usługa IoT Edge ma rozszerzenia do obsługi narzędzi DevOps, w tym Azure DevOps, Projektów Azure DevOps i Jenkins. Jeśli chcesz dostosować istniejący potok lub użyć innego narzędzia DevOps, takiego jak CircleCI lub TravisCI, możesz to zrobić za pomocą funkcji interfejsu wiersza polecenia dołączonych do narzędzia deweloperskiego IoT Edge.

Aby uzyskać więcej informacji, wskazówek i przykładów, zobacz następujące strony:

* [Ciągła integracja i ciągłe wdrażanie usługi Azure IoT Edge](how-to-ci-cd.md)
* [Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla usługi IoT Edge za pomocą projektów devops platformy Azure](how-to-devops-project.md)
* [Wtyczka usługi Azure IoT Edge Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [Repozytorium IoT Edge DevOps GitHub](https://github.com/toolboc/IoTEdge-DevOps)
