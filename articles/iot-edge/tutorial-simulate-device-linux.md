---
title: Symulacja usługi Azure IoT Edge w systemie Linux | Microsoft Docs
description: Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge na urządzeniu symulowanym w systemie Linux i wdrażanie pierwszego modułu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0b8b2658af9173cea6a7cdcb0147c7b0dc13a455
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631000"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Wdrażanie usługi Azure IoT Edge na urządzeniu symulowanym w systemie Linux lub MacOS — wersja zapoznawcza

Usługa Azure IoT Edge umożliwia wykonywanie analiz i przetwarzanie danych na urządzeniach, bez konieczności wypychania wszystkich danych do chmury. W samouczkach usługi IoT Edge przedstawiono sposób wdrażania różnych typów modułów tworzonych przy użyciu usług platformy Azure lub własnego kodu — ale najpierw potrzebne jest urządzenie do ich testowania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub
2. Rejestrowanie urządzenia usługi IoT Edge
3. Uruchamianie środowiska uruchomieniowego usługi IoT Edge
4. Wdrażanie modułu

![Architektura samouczka][2]

Urządzenie symulowane tworzone podczas pracy z tym samouczkiem to monitor generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy tych danych na potrzeby biznesowe. 

## <a name="prerequisites"></a>Wymagania wstępne

Podczas pracy z tym samouczkiem będziesz korzystać ze swojego komputera lub z maszyny wirtualnej jak z urządzenia Internetu rzeczy. Aby używać maszyny jako urządzenia usługi IoT Edge, potrzebne są następujące usługi:

* Menedżer pakietów pip języka Python do zainstalowania środowiska uruchomieniowego usługi IoT Edge.
   * Linux: `sudo apt-get install python-pip`.
     * _Należy pamiętać, że w przypadku niektórych dystrybucji (takich jak Raspbian) może być również konieczne uaktualnienie niektórych pakietów pip i zainstalowanie dodatkowych zależności:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Aplikacja Docker do uruchamiania modułów usługi IoT Edge
   * [Zainstaluj aplikację Docker dla systemu Linux][lnk-docker-ubuntu] i upewnij się, że jest uruchomiona. 
   * [Zainstaluj aplikację Docker dla komputerów Mac][lnk-docker-mac] i upewnij się, że jest uruchomiona. 

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Rozpocznij pracę z samouczkiem od utworzenia centrum IoT Hub.
![Tworzenie centrum IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT Hub.
![Rejestrowanie urządzenia][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu. 
![Rejestrowanie urządzenia][5]

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Zawiera dwa moduły. **Agent usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. Po skonfigurowaniu środowiska uruchomieniowego na nowym urządzeniu najpierw zostanie uruchomiony tylko agent usługi IoT Edge. Centrum usługi IoT Edge będzie używane później, po wdrożeniu modułu. 

Na komputerze, na którym ma działać urządzenie usługi IoT Edge, pobierz skrypt sterujący usługi IoT Edge:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Skonfiguruj środowisko uruchomieniowe za pomocą parametrów połączenia urządzenia usługi IoT Edge z poprzedniej sekcji:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe:
```cmd
sudo iotedgectl start
```

Sprawdź w aplikacji Docker, czy agent usługi IoT Edge jest uruchomiony jako moduł:
```cmd
sudo docker ps
```

![Wyświetlanie modułu edgeAgent w aplikacji Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Rejestrowanie urządzenia][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym samouczku utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć na potrzeby samouczków. 

Otwórz ponownie wiersz polecenia na komputerze z uruchomionym urządzeniem symulowanym. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge:

```cmd
sudo docker ps
```

![Wyświetlanie trzech modułów na urządzeniu](./media/tutorial-simulate-device-linux/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury:

```cmd
sudo docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-linux/docker-logs.png)

Możesz też wyświetlić dane telemetryczne wysyłane przez urządzenie przy użyciu [narzędzia eksploratora usługi IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono nowe urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie symulowane generujące dane pierwotne dotyczące jego otoczenia. 

Wykonanie czynności przedstawionych w tym samouczku jest wymagane do pracy z wszystkimi pozostałymi samouczkami usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie kodu w języku C# jako modułu](tutorial-csharp-module.md)


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
