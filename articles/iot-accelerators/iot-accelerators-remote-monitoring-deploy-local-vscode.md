---
title: Wdrażanie rozwiązania zdalnego monitorowania lokalnie — kod programu Visual Studio — Azure | Dokumenty firmy Microsoft
description: W tym przewodniku przedstawiono sposób wdrażania akceleratora rozwiązania zdalnego monitorowania na komputerze lokalnym przy użyciu programu Visual Studio Code do testowania i tworzenia.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890955"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Wdrażanie akceleratora rozwiązań do zdalnego monitorowania lokalnie — Kod programu Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań zdalnego monitorowania na komputerze lokalnym w celu testowania i tworzenia. Dowiesz się, jak uruchomić mikrousług w programie Visual Studio Code. Wdrożenie mikrousług lokalnych używa następujących usług w chmurze: Usługa IoT Hub, usługa Cosmos DB, usługa Azure Streaming Analytics i usługa Azure Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązań zdalnego monitorowania, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja maszyny

Aby ukończyć wdrożenie lokalne, na lokalnym komputerze deweloperskim są zainstalowane następujące narzędzia:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Kod programu Visual Studio](https://code.visualstudio.com/)
* [Rozszerzenie C# programu VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym interfejsu wiersza polecenia komputera PCS używanego do tworzenia zasobów platformy Azure. Nie używaj pliku Node.js w wersji 10

> [!NOTE]
> Visual Studio Code jest dostępny dla systemów Windows, Mac i Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji należy uruchomić mikrousług monitorowania zdalnego. Natywnie uruchamiasz internetowy interfejs użytkownika, usługę symulacji urządzeń w programie Docker i mikrousług w programie Visual Studio Code.

### <a name="build-the-code"></a>Kompilowanie kod

Przejdź do azure-iot-pcs-remote-monitoring-dotnet\services w wierszu polecenia i uruchom następujące polecenia, aby utworzyć kod.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdrażanie wszystkich innych mikrousług na komputerze lokalnym

W poniższych krokach pokazano, jak uruchomić mikrousług monitorowania zdalnego w programie Visual Studio Code:

1. Uruchom program Visual Studio Code.
1. W programie VS Code otwórz folder **azure-iot-pcs-remote-monitoring-dotnet.**
1. Utwórz nowy folder o nazwie **.vscode** w folderze **azure-iot-pcs-remote-monitoring-dotnet.**
1. Skopiuj pliki **launch.json** i **tasks.json** z services\scripts\local\launch\idesettings\vscode do właśnie utworzonego folderu **vscode.**
1. Otwórz **panel Debugowania** w programie VS Code i uruchom konfigurację **Uruchom wszystkie mikrousługi.** Ta konfiguracja uruchamia mikrousługi symulacji urządzenia w programie Docker i uruchamia inne mikrousług w debugerze.

Dane wyjściowe z **uruchomionego uruchamiania wszystkich mikropodłączeń** w konsoli debugowania wygląda następująco:

[![Wdrażanie-mikrousługi lokalne](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku należy uruchomić interfejs użytkownika sieci Web. Przejdź do **folderu azure-iot-pcs-remote-monitoring-dotnet\webui** w kopii lokalnej i uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu uruchamiania przeglądarka wyświetla stronę **\/http: /localhost:3000/dashboard**. Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujący krok.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie NGINX

Skonfiguruj odwrotny serwer proxy, aby połączyć aplikację sieci web i mikrousług uruchomionych na komputerze lokalnym:

* Skopiuj plik **nginx.conf** z **folderu webui\scripts\localhost** do katalogu **nginx\conf** install.
* Uruchom **nginx**.

Aby uzyskać więcej informacji na temat uruchamiania **nginx**, zobacz [nginx dla systemu Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Łączenie się z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania zdalnego monitorowania, przejdź do http:\//localhost:9000 w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania usunąć usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure Portal](https://ms.portal.azure.com) i usuń grupę zasobów utworzoną przez skrypt **start.cmd.**

Można również usunąć lokalną kopię repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do zdalnego monitorowania, następnym krokiem jest [zbadanie możliwości pulpitu nawigacyjnego rozwiązania.](quickstart-remote-monitoring-deploy.md)
