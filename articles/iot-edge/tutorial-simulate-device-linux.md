---
title: Symulowanie Azure IoT Edge w systemie Linux | Dokumentacja firmy Microsoft
description: Instalowanie środowiska uruchomieniowego Azure IoT Edge na symulowane urządzenie w systemie Linux i wdrażanie pierwszego modułu
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65a3f6d71c0c0d92f703a5d48760dd348c726ba4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Wdrażanie usługi Azure IoT krawędzi na symulowane urządzenie w systemie Linux lub MacOS — podglądu

Krawędź IoT Azure umożliwia przeprowadzanie analizy i przetwarzania danych na urządzeniach, zamiast do dystrybuowania wszystkich danych w chmurze. Samouczki krawędzi IoT przedstawiają sposób wdrażania różnego rodzaju moduły skompilowane z usług Azure lub kodu niestandardowego, ale najpierw należy urządzenia do testowania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

1. Tworzenie Centrum IoT
2. Zarejestruj urządzenia IoT
3. Uruchom środowisko uruchomieniowe krawędzi IoT
4. Wdrażanie modułu

![Architektura samouczka][2]

Symulowane urządzenie, które możesz utworzyć w tym samouczku jest monitor, który generuje temperatury, wilgotności i wykorzystania danych. Innych samouczków usługi Azure IoT krawędzi zależą od pracy, w tym miejscu poprzez wdrożenie modułów, które analiz biznesowych. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku korzysta z komputera lub maszyny wirtualnej, takie jak urządzenie Internetu rzeczy. Aby wyłączyć komputer do urządzenia IoT, wymagane są następujące usługi:

* Python pip, aby zainstalować środowisko uruchomieniowe IoT krawędzi.
   * Linux: `sudo apt-get install python-pip`.
     * _Należy pamiętać, że w niektórych dystrybucji (np. Raspbian), może być również konieczne uaktualnienie niektórych pakietów pip i zainstalować dodatkowe zależności:_
     ```
     sudo pip install --upgrade setuptools pip
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker do uruchamiania krawędzi IoT modułów
   * [Zainstaluj dla systemu Linux Docker] [ lnk-docker-ubuntu] i upewnij się, że jest uruchomiona. 
   * [Zainstaluj Docker dla komputerów Mac] [ lnk-docker-mac] i upewnij się, że jest uruchomiona. 

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Uruchom samouczek tworzenia Centrum IoT.
![Tworzenie Centrum IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Zarejestruj urządzenia IoT

Zarejestruj urządzenia IoT z nowo utworzonego Centrum IoT.
![Zarejestruj urządzenie][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Zainstaluj i uruchom środowisko uruchomieniowe krawędzi IoT

Zainstaluj i uruchom środowiska uruchomieniowego Azure IoT Edge na urządzeniu. 
![Zarejestruj urządzenie][5]

Środowisko uruchomieniowe krawędzi IoT jest wdrażana na wszystkich urządzeniach IoT krawędzi. Zawiera dwa moduły. **IoT krawędź agent** ułatwia wdrażanie i monitorowanie modułów na urządzeniu IoT krawędzi. **Centrum IoT krawędzi** zarządza komunikacji między modułami na urządzeniu IoT krawędzi i między urządzeniami a Centrum IoT. Po skonfigurowaniu środowiska uruchomieniowego na nowe urządzenia tylko agent krawędzi IoT rozpocznie się na początku. Centrum IoT krawędzi jest dostarczany później, podczas wdrażania modułu. 

Na komputerze, na którym będzie uruchamiane urządzenie brzegowe IoT Pobierz skrypt kontroli krawędzi IoT:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Skonfiguruj środowisko uruchomieniowe za pomocą parametrów połączenia urządzenia IoT krawędzi z poprzedniej sekcji:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe:
```cmd
sudo iotedgectl start
```

Docker wyboru, aby zobaczyć, czy agent krawędzi IoT jest uruchomiony jako moduł:
```cmd
sudo docker ps
```

![Zobacz edgeAgent w Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzanie urządzeniem krawędzi IoT Azure w chmurze, aby wdrożyć moduł, który będzie wysyłać dane telemetryczne z Centrum IoT.
![Zarejestruj urządzenie][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Widok wygenerowany danych

W tym samouczku zostało utworzone nowe urządzenie brzegowe IoT i na nim zainstalowany środowiska uruchomieniowego IoT krawędzi. Następnie użyto portalu Azure do umieszczenia krawędzi IoT modułu do uruchomienia na urządzeniu bez konieczności zmiany na urządzeniu. W takim przypadku moduł, który zostanie przypisany tworzy dane środowiska, używanego programu samouczków. 

Otwórz wiersz polecenia na komputerze z uruchomionym symulowane urządzenie ponownie. Upewnij się, że moduł wdrożonych w chmurze jest uruchomiona na urządzeniu IoT krawędzi:

```cmd
sudo docker ps
```

![Wyświetl trzech modułów na urządzeniu](./media/tutorial-simulate-device-linux/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury:

```cmd
sudo docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-linux/docker-logs.png)

Można również wyświetlić dane telemetryczne, wysyła urządzenia przy użyciu [narzędzia explorer Centrum IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony nowe urządzenie brzegowe IoT i interfejs chmury Azure IoT krawędzi można wdrożyć kod na urządzeniu. Pojawiły się symulowane urządzenie generowania nieprzetworzone dane o jego środowisku. 

W tym samouczku jest wstępnym dla wszystkich innych samouczków IoT krawędzi. Można kontynuować do wszystkich innych samouczków, aby dowiedzieć się, jak Azure IoT krawędzi można przekształcić danych biznesowych na krawędzi.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie kodu C# jako moduł](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
