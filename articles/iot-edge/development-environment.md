---
title: Środowisko deweloperskie w usłudze Azure IoT Edge | Dokumentacja firmy Microsoft
description: Informacje o obsługiwanych systemach i narzędzi programistycznych firmy Microsoft, które pomogą utworzyć moduły usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6fc2af0cbe770ee787da757966bbc1647717e5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66302679"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Przygotuj swój rozwój i środowisko testowe dla usługi IoT Edge

Usługa Azure IoT Edge przenosi istniejącej logiki biznesowej do urządzeń działających na urządzeniach brzegowych. Aby przygotować aplikacje i obciążenia, aby był uruchamiany jako [moduły usługi IoT Edge](iot-edge-modules.md), musisz utworzyć je jako kontenery. Ten artykuł zawiera wskazówki dotyczące sposobu konfigurowania środowiska deweloperskiego, dzięki czemu można pomyślnie utworzyć rozwiązanie IoT Edge. Po utworzeniu środowiska deweloperskiego, konfigurowanie, następnie możesz dowiedzieć się jak [tworzyć własne moduły usługi IoT Edge](module-development.md).

W dowolnym rozwiązaniu IoT Edge istnieją co najmniej dwóch maszyn do rozważenia. Jeden jest urządzenie usługi IoT Edge (lub urządzenia) siebie, który jest uruchamiany moduł usługi IoT Edge. Druga to na komputerze deweloperskim, którego używasz do tworzenia, testowania i wdrażania modułów. Ten artykuł koncentruje się głównie na komputerze deweloperskim. Do celów testowych, dwa komputery może być taki sam. Można uruchomić usługi IoT Edge na komputerze deweloperskim i wdrażać moduły do niego.

## <a name="operating-system"></a>System operacyjny

Usługa Azure IoT Edge działa na określony zbiór [obsługiwane systemy operacyjne](support.md). Do tworzenia, IoT Edge, możesz użyć większość systemów operacyjnych, które można uruchomić aparatu kontenera. Aparat kontenera jest wymagana na komputerze deweloperskim, aby skompilować moduły jako kontenery i odesłać je do rejestru kontenerów. 

Jeśli na komputerze deweloperskim, nie można uruchomić usługi Azure IoT Edge, w stanie kontynuować działanie w tym artykule, aby dowiedzieć się więcej na temat [narzędzia do testowania](#testing-tools) , ułatwić testowanie i debugowanie lokalnie. 

System operacyjny na komputerze deweloperskim, nie ma zgodny system operacyjny urządzenia usługi IoT Edge. Jednak system operacyjny kontenera musi być spójna pomiędzy komputerem deweloperskim i urządzenie usługi IoT Edge. Można na przykład tworzenie modułów na komputerze Windows i wdrożyć je na urządzeniu z systemem Linux. Komputer Windows musi się do uruchamiania kontenerów systemu Linux do tworzenia modułów dla tego urządzenia z systemem Linux. 

## <a name="container-engine"></a>Aparat kontenera

Centralnej pojęcia usługi IoT Edge to, że upakowanie do kontenerów można wdrożyć zdalnie logiki biznesowej i w chmurze na urządzeniach. Aby utworzyć kontenery, należy aparatu kontenera na komputerze deweloperskim. 

Aparat tylko obsługiwane kontenerów dla urządzenia usługi IoT Edge w środowisku produkcyjnym jest Moby. Jednak każdy silnik kontenera zgodny z otwartych inicjatywy kontenera, takich jak Docker, jest w stanie tworzenia obrazów modułu usługi IoT Edge. 

## <a name="development-tools"></a>Narzędzia programistyczne

Visual Studio i Visual Studio Code ma dodatek rozszerzenia pomagające w tworzeniu rozwiązań IoT Edge. Rozszerzenia te zawierają szablony specyficzne dla języka, aby utworzyć i wdrożyć nowe scenariusze IoT Edge. Rozszerzenia usługi Azure IoT Edge, aby uzyskać Pomoc programu Visual Studio i Visual Studio Code kodu, kompilacji, wdrażania i debugowania rozwiązań usługi IoT Edge. Można utworzyć całe rozwiązanie IoT Edge, która zawiera wiele modułów i rozszerzenia automatycznie aktualizuje szablon manifestu wdrożenia do każdego nowego dodawania modułu. Rozszerzenia można również zarządzać z urządzeń IoT z poziomu programu Visual Studio lub Visual Studio Code. Wdrażać moduły na urządzeniu, a następnie monitorować stan i Wyświetl komunikaty przychodzące w usłudze IoT Hub. Oba rozszerzenia przy użyciu [narzędzia deweloperskiego IoT EdgeHub](#iot-edgehub-dev-tool) aby umożliwić lokalne uruchamianie i debugowanie modułów na komputerze deweloperskim także. 

Jeśli wolisz tworzyć przy użyciu innych edytorów lub interfejsu wiersza polecenia narzędzia deweloperskie usługi Azure IoT Edge zapewnia polecenia możesz rozwijać i przetestować z poziomu wiersza polecenia. Można tworzyć nowe usługi IoT Edge scenariusze, kompilowanie obrazów modułu, uruchom moduły w symulatorze i monitorowania komunikatów wysyłanych do usługi IoT Hub. 

### <a name="visual-studio-code-extension"></a>Rozszerzenie programu Visual Studio Code

Rozszerzenia programu Visual Studio Code dla usługi Azure IoT Edge zapewnia usługi IoT Edge modułu szablony utworzone w tym C, języków programowania C#, Java, Node.js i Python, a także usługa Azure functions w C#. 

Aby uzyskać więcej informacji oraz aby pobrać, zobacz [narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Oprócz rozszerzenia usługi IoT Edge może okazać się przydatne do zainstalowania dodatkowych rozszerzeń dla rozwoju. Na przykład, można użyć [obsługę platformy Docker dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) do zarządzania obrazami, kontenerów i rejestrów. Ponadto wszystkie ważniejsze języki obsługiwane są rozszerzenia programu Visual Studio Code, które mogą pomóc, gdy tworzysz modułów. 

#### <a name="prerequisites"></a>Wymagania wstępne

Szablony modułu dla niektórych języków i usług mają wymagania wstępne, które są niezbędne do tworzenia folderów projektu na komputerze deweloperskim z programem Visual Studio Code.

| Szablon modułu | Wymagania wstępne |
| --------------- | ------------ |
| Azure Functions | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Usługa Git](https://git-scm.com/) |
| C# | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Zmienna środowiskowa JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generator modułu w usłudze Azure IoT Edge w środowisku Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Narzędzie PIP](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Usługa Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Rozszerzenie programu Visual Studio 2017/2019 r

Narzędzia usługi Azure IoT Edge dla programu Visual Studio zapewnia usługi IoT Edge szablon modułu oparta na C# i C. 

Aby uzyskać więcej informacji oraz aby pobrać, zobacz [narzędzia usługi Azure IoT Edge dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) lub [narzędzia usługi Azure IoT Edge dla programu Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Narzędzia deweloperskie usługi IoT Edge

Narzędzia deweloperskie usługi Azure IoT Edge upraszcza opracowywanie usługi IoT Edge za pomocą funkcji wiersza polecenia. To narzędzie zapewnia polecenia interfejsu wiersza polecenia do tworzenia, debugowania i testowania modułów. Narzędzia deweloperskiego usługi IoT Edge projektów w programach systemu dla deweloperów, czy został zainstalowany ręcznie zależności na komputerze lub korzystasz z kontenera deweloperów usługi IoT Edge. 

Aby uzyskać więcej informacji i rozpocząć pracę, zobacz [wiki narzędzia deweloperskie usługi IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Narzędzia do testowania

Istnieje kilka narzędzi do testowania ułatwiające symulowanie urządzenia usługi IoT Edge lub jego debugowania modułów, bardziej wydajne. W poniższej tabeli przedstawiono ogólne porównanie narzędzi, a następnie poszczególne sekcje opisują każde narzędzie bardziej szczegółowo. 

Tylko środowisko uruchomieniowe usługi IoT Edge jest obsługiwana w przypadku wdrożeń produkcyjnych, ale następujące narzędzia umożliwiają symulowanie i łatwo utworzyć usługi IoT Edge urządzenia do tworzenia i testowania. Narzędzia te nie są wzajemnie się wykluczają, ale mogą współpracować ze sobą środowisko programowania pełną. 

| Narzędzie | Tzw. | Obsługiwane platformy | Najlepsze dla |
| ---- | ------------- | ------------------- | --------- |
| Narzędzia deweloperskie IoT EdgeHub  | iotedgehubdev | System Windows, Linux, MacOS | Symulowanie urządzenia do debugowania modułów. |
| Kontener deweloperów usługi IoT Edge | microsoft/iotedgedev | System Windows, Linux, MacOS | Tworzenie, bez konieczności instalowania zależności. |
| Środowisko uruchomieniowe usługi IoT Edge w kontenerze | iotedgec | Windows, Linux, MacOS, ARM | Testowanie na urządzeniu, które mogą nie obsługiwać środowiska uruchomieniowego. |
| Kontener urządzeń usługi IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testowanie scenariusza za pomocą wielu urządzeń usługi IoT Edge na dużą skalę. |

### <a name="iot-edgehub-dev-tool"></a>Narzędzia deweloperskie IoT EdgeHub

Narzędzia deweloperskie Azure IoT EdgeHub udostępnia lokalne środowisko programowania i debugowania. Narzędzie ułatwia rozpoczęcie moduły usługi IoT Edge bez środowiska uruchomieniowego usługi IoT Edge, aby można było utworzyć, opracowywanie, testowanie, uruchamianie i debugowanie moduły usługi IoT Edge oraz lokalnie rozwiązania. Nie masz wypychanie obrazów do rejestru kontenerów i wdrażanie ich na urządzenie do testowania.

Narzędzia deweloperskiego IoT EdgeHub został zaprojektowany w celu współdziałają z rozszerzeniami programu Visual Studio i Visual Studio Code, a także za pomocą narzędzia deweloperskie usługi IoT Edge. Go obsługuje wewnętrznej pętli tworzenia kodu, a także testowanie zewnętrzna pętla, więc integruje się z narzędziami DevOps za. 

Aby uzyskać więcej informacji i zainstalować, zobacz [narzędzia deweloperskie usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Kontener deweloperów usługi IoT Edge

Kontener deweloperów usługi Azure IoT Edge jest kontenerze platformy Docker, który zawiera wszystkie zależności, które są potrzebne do tworzenia aplikacji IoT Edge. Ten kontener ułatwia rozpoczynanie pracy z jakikolwiek język chcesz rozwijać, w tym C#, Python, Node.js i Java. Wszystko, czego potrzebujesz, aby zainstalować to aparat kontenera, takich jak Docker lub Moby, w celu ściągnięcia kontenera na maszynie deweloperskiej. 

Aby uzyskać więcej informacji, zobacz [kontenera deweloperów usługi Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Środowisko uruchomieniowe usługi IoT Edge w kontenerze

Środowisko uruchomieniowe usługi IoT Edge w kontenerze zapewnia pełne środowisko uruchomieniowe, która przyjmuje parametry połączenia urządzenia jako zmienną środowiskową. Ten kontener umożliwia testowanie moduły usługi IoT Edge i scenariuszy w systemie, które mogą nie obsługiwać środowiska uruchomieniowego natywnie, takich jak MacOS. Wszystkie moduły, które wdrażasz zostanie uruchomiony poza kontener środowiska uruchomieniowego. Jeśli chcesz, środowisko uruchomieniowe i wszelkie wdrożone moduły, które istniały w ramach tego samego kontenera, należy rozważyć kontener urządzeń usługi IoT Edge.

Aby uzyskać więcej informacji, zobacz [uruchamiania usługi Azure IoT Edge w kontenerze](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Kontener urządzeń usługi IoT Edge

Kontener urządzeń usługi IoT Edge jest kompletne urządzenie usługi IoT Edge, gotowe do uruchomienia na dowolnym komputerze z aparatem kontenera. Kontener urządzeń zawiera środowisko uruchomieniowe usługi IoT Edge i aparat container, sam. Każde wystąpienie kontenera jest w pełni funkcjonalne własnym aprowizacji urządzenia IoT Edge. Kontener urządzeń obsługuje zdalne debugowanie modułów, tak długo, jak istnieje trasa sieci do modułu. Kontener urządzeń jest dobry do szybkiego tworzenia dużej liczby usługi IoT Edge urządzenia, aby przetestować scenariusze na dużą skalę lub potoki usługi Azure. Obsługuje ona również wdrażanie w usłudze kubernetes za pomocą narzędzia helm. 

Aby uzyskać więcej informacji, zobacz [kontener urządzeń usługi Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Narzędzia DevOps

Gdy wszystko będzie gotowe do opracowywania rozwiązań na dużą skalę na potrzeby scenariuszy produkcyjnych rozbudowane, korzystaj z nowoczesnego zasadami metodyki DevOps, łącznie z automatyzacji, monitorowanie i procesy usprawnione inżynierii oprogramowania. Usługi IoT Edge ma rozszerzeń do obsługi narzędzi DevOps, takich jak DevOps platformy Azure, usługi Azure DevOps Projects i Jenkins. Aby dostosować istniejący potok lub użyć innego narzędzia DevOps, takich jak CircleCI lub rozwiązania TravisCI, możesz to zrobić za pomocą funkcji interfejsu wiersza polecenia zawarte w narzędzia deweloperskiego usługi IoT Edge.

Aby uzyskać dodatkowe informacje, wskazówki i przykłady zobacz następujące strony:
* [Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge](how-to-ci-cd.md)
* [Tworzenie potoku CI/CD dla usługi IoT Edge przy użyciu usługi Azure DevOps Projects](how-to-devops-project.md)
* [Wtyczka usługi Azure IoT Edge Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [Repozytorium IoT Edge metodyki DevOps w witrynie GitHub](https://github.com/toolboc/IoTEdge-DevOps)
