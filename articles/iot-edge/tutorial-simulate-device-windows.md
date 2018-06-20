---
title: Symulacja usługi Azure IoT Edge w systemie Windows | Microsoft Docs
description: Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge na urządzeniu symulowanym w systemie Windows i wdrażanie pierwszego modułu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7ad99a49a578de4997a2d76d48da33aba6847f3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631194"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Wdrażanie usługi Azure IoT Edge na urządzeniu symulowanym w systemie Windows — wersja zapoznawcza

Usługa Azure IoT Edge umożliwia wykonywanie analiz i przetwarzanie danych na urządzeniach, bez konieczności wypychania wszystkich danych do chmury. W samouczkach usługi IoT Edge przedstawiono sposób wdrażania różnych typów modułów tworzonych przy użyciu usług platformy Azure lub własnego kodu — ale najpierw potrzebne jest urządzenie do ich testowania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub
2. Rejestrowanie urządzenia usługi IoT Edge
3. Uruchamianie środowiska uruchomieniowego usługi IoT Edge
4. Wdrażanie modułu

![Architektura samouczka][2]

Urządzenie symulowane tworzone podczas pracy z tym samouczkiem to monitor turbiny wiatrowej generujący dane dotyczące temperatury, wilgotności i ciśnienia. Interesują Cię te dane, ponieważ Twoje turbiny działają na różnych poziomach wydajności w zależności od warunków pogodowych. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy tych danych na potrzeby biznesowe. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że używasz komputera lub maszyny wirtualnej z uruchomionym systemem Windows do symulacji urządzenia Internetu rzeczy. 

>[!TIP]
>Jeśli korzystasz z systemu Windows na maszynie wirtualnej, włącz [zagnieżdżoną wirtualizację][lnk-nested] i przydziel co najmniej 2 GB pamięci. 

1. Upewnij się, że używasz obsługiwanej wersji systemu Windows:
   * Windows 10 
   * Windows Server
2. Zainstaluj aplikację [Docker for Windows][lnk-docker] i upewnij się, że jest uruchomiona.
3. Zainstaluj środowisko [Python w systemie Windows][lnk-python] i upewnij się, że możesz użyć polecenia pip. Ten samouczek przetestowano przy użyciu wersji środowiska Python >=2.7.9 i >=3.5.4.  
4. Uruchom następujące polecenie, aby pobrać skrypt kontroli usługi IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Usługa Azure IoT Edge może uruchamiać kontenery systemów Windows lub Linux. Jeśli korzystasz z jednej z następujących wersji systemu Windows, możesz używać kontenerów systemu Windows:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (kompilacja 16299)
>    * Windows IoT Core (kompilacja 16299) na urządzeniu opartym na architekturze x64
>
> W przypadku systemu Windows IoT Core postępuj zgodnie z instrukcjami w temacie [Install the IoT Edge runtime on Windows IoT Core][lnk-install-iotcore] (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemie Windows IoT Core). W przeciwnym razie po prostu [skonfiguruj platformę Docker do korzystania z kontenerów systemu Windows][lnk-docker-containers]. Użyj następującego polecenia, aby zweryfikować wymagania wstępne:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Rozpocznij pracę z samouczkiem od utworzenia centrum IoT Hub.
![Tworzenie centrum IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT Hub.
![Rejestrowanie urządzenia][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu. 
![Rejestrowanie urządzenia][5]

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Zawiera dwa moduły. **Agent usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. Po skonfigurowaniu środowiska uruchomieniowego na nowym urządzeniu najpierw zostanie uruchomiony tylko agent usługi IoT Edge. Centrum usługi IoT Edge będzie używane później, po wdrożeniu modułu. 


Skonfiguruj środowisko uruchomieniowe za pomocą parametrów połączenia urządzenia usługi IoT Edge z poprzedniej sekcji.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe.

```cmd
iotedgectl start
```

Sprawdź aplikację Docker, aby się upewnić, że agent usługi IoT Edge jest uruchomiony jako moduł.

```cmd
docker ps
```

![Wyświetlanie agenta usługi Edge w aplikacji Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Rejestrowanie urządzenia][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym samouczku utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć na potrzeby samouczków. 

Otwórz ponownie wiersz polecenia na komputerze z uruchomionym urządzeniem symulowanym. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge. 

```cmd
docker ps
```

![Wyświetlanie trzech modułów na urządzeniu](./media/tutorial-simulate-device-windows/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury. 

```cmd
docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-windows/docker-logs.png)

Dane telemetryczne wysyłane przez urządzenie można również wyświetlić przy użyciu [narzędzia eksploratora usługi IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono nowe urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie symulowane generujące dane pierwotne dotyczące jego otoczenia. 

Wykonanie czynności przedstawionych w tym samouczku jest wymagane do pracy z wszystkimi pozostałymi samouczkami usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie kodu w języku C# jako modułu](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md