---
title: Szybki Start Azure IoT krawędzi + systemu Linux | Dokumentacja firmy Microsoft
description: Wypróbować usługę Azure IoT krawędzi, uruchamiając analytics na urządzeniu symulowane krawędzi
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 750f09c91a086b22df5e7557e4b6fc6a763499e2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>Szybki Start: Wdrażanie modułu IoT krawędź pierwszego na urządzeniu z systemem Linux lub Mac — wersja zapoznawcza

Usługa Azure IoT krawędzi przenosi mocy chmury urządzeń Internetu rzeczy. W tym temacie Dowiedz się, jak użyć interfejsu chmury do zdalnego wdrożenia wbudowane kodu do urządzenia IoT.

Jeśli nie masz aktywnych subskrypcji platformy Azure, Utwórz [bezpłatne konto] [ lnk-account] przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Ta opcja szybkiego startu korzysta z komputera lub maszyny wirtualnej, takie jak urządzenie Internetu rzeczy. Aby wyłączyć komputer do urządzenia IoT, wymagane są następujące usługi:

* Python pip, aby zainstalować środowisko uruchomieniowe IoT krawędzi.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker do uruchamiania krawędzi IoT modułów
   * [Zainstaluj dla systemu Linux Docker] [ lnk-docker-ubuntu] i upewnij się, że jest uruchomiona. 
   * [Zainstaluj Docker dla komputerów Mac] [ lnk-docker-mac] i upewnij się, że jest uruchomiona. 

## <a name="create-an-iot-hub-with-azure-cli"></a>Tworzenie Centrum IoT z wiersza polecenia platformy Azure

Utwórz Centrum IoT w Twojej subskrypcji platformy Azure. Poziom wolnego Centrum IoT działa w przypadku tego przewodnika Szybki Start. Jeśli używano Centrum IoT w przeszłości i już koncentratora wolnego utworzony, można pominąć tę sekcję i przejdź do [zarejestrować urządzenia IoT][anchor-register]. Każda subskrypcja może mieć tylko jeden bezpłatne Centrum IoT. 

1. Zaloguj się w witrynie [Azure Portal][lnk-portal]. 
1. Wybierz **powłoki chmury** przycisku. 

   ![Przycisk powłoki w chmurze][1]

1. Utwórz grupę zasobów. Poniższy kod tworzy grupę zasobów o nazwie **IoTEdge** w **zachodnie stany USA** regionu:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Utwórz Centrum IoT w nowej grupy zasobów. Poniższy kod tworzy bezpłatny **F1** koncentratora o nazwie **MyIotHub** w grupie zasobów **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Zarejestruj urządzenia IoT

Tworzenie tożsamości urządzenia symulowane urządzenie, dzięki czemu może komunikować się z Centrum IoT. Ponieważ urządzenia IoT brzegowe zachowują się i mogą być zarządzane inaczej niż typowe urządzenia IoT, zadeklarować to być urządzenia IoT od początku. 

1. W portalu Azure przejdź do Centrum IoT.
1. Wybierz **IoT krawędzi (wersja zapoznawcza)**.
1. Wybierz **dodać IoT urządzenia**.
1. Nadaj symulowane urządzenie identyfikator unikatowy urządzenia.
1. Wybierz **zapisać** Aby dodać własne urządzenie.
1. Wybierz nowe urządzenie z listy urządzeń. 
1. Skopiuj wartość **parametry połączenia — klucz podstawowy** i zapisz go. Ta wartość będzie używane do konfigurowania środowiska uruchomieniowego IoT krawędzi w następnej sekcji. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Zainstaluj i uruchom środowisko uruchomieniowe krawędzi IoT

Środowisko uruchomieniowe krawędzi IoT jest wdrażana na wszystkich urządzeniach IoT krawędzi. Zawiera dwa moduły. Najpierw agenta krawędzi IoT ułatwia wdrażanie i monitorowanie modułów na urządzeniu IoT krawędzi. Po drugie Centrum IoT krawędzi zarządza komunikacji między modułami na urządzeniu IoT krawędzi i między urządzeniami a Centrum IoT. 

Na komputerze, na którym będzie uruchamiane urządzenie brzegowe IoT Pobierz skrypt kontroli krawędzi IoT:
```bash
sudo pip install -U azure-iot-edge-runtime-ctl
```

Skonfiguruj środowisko uruchomieniowe za pomocą parametrów połączenia urządzenia IoT krawędzi z poprzedniej sekcji:
```bash
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe:
```bash
sudo iotedgectl start
```

Docker wyboru, aby zobaczyć, czy agent krawędzi IoT jest uruchomiony jako moduł:
```bash
sudo docker ps
```

![Zobacz edgeAgent w Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Widok wygenerowany danych

W tego przewodnika Szybki Start utworzyć nowe urządzenie brzegowe IoT, a na nim zainstalowany środowiska uruchomieniowego IoT krawędzi. Następnie użyto portalu Azure do umieszczenia krawędzi IoT modułu do uruchomienia na urządzeniu bez konieczności zmiany na urządzeniu. W takim przypadku moduł, który zostanie przypisany tworzy dane środowiska, używanego programu samouczków. 

Otwórz wiersz polecenia na komputerze z uruchomionym symulowane urządzenie ponownie. Upewnij się, że moduł wdrożonych w chmurze jest uruchomiona na urządzeniu IoT krawędzi:

```bash
sudo docker ps
```

![Wyświetl trzech modułów na urządzeniu](./media/tutorial-simulate-device-linux/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury:

```bash
sudo docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-linux/docker-logs.png)

Można również wyświetlić dane telemetryczne, wysyła urządzenia przy użyciu [narzędzia explorer Centrum IoT][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć symulowane urządzenie, które zostały utworzone, oraz kontenerów Docker, które zostały uruchomione dla każdego modułu, użyj następującego polecenia: 

```bash
sudo iotedgectl uninstall
```

Podczas tworzenia Centrum IoT nie są już potrzebne, można użyć [usuwania Centrum iot az] [ lnk-delete] polecenie, aby usunąć zasób i wszystkie skojarzone z nią urządzenia:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Kolejne kroki

Wiesz, jak wdrożyć moduł krawędzi IoT urządzenia IoT. Teraz spróbuj wdrażanie różnych typów usług Azure modułów, dzięki czemu można analizować dane na krawędzi. 

* [Wdrożyć własny kod jako moduł](tutorial-csharp-module.md)
* [Wdrażanie funkcji platformy Azure jako moduł](tutorial-deploy-function.md)
* [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
