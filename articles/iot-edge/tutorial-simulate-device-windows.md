---
title: Symulowanie Azure IoT Edge w systemie Windows | Dokumentacja firmy Microsoft
description: Instalowanie środowiska uruchomieniowego Azure IoT Edge na symulowane urządzenie w systemie Windows i wdrażanie pierwszego modułu
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 213a0e7cebda6a8b89ef460799cbec477b487a64
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Wdrażanie usługi Azure IoT krawędzi na symulowane urządzenie w systemie Windows — podglądu

Krawędź IoT Azure umożliwia przeprowadzanie analizy i przetwarzania danych na urządzeniach, zamiast do dystrybuowania wszystkich danych w chmurze. Samouczki krawędzi IoT przedstawiają sposób wdrażania różnego rodzaju moduły skompilowane z usług Azure lub kodu niestandardowego, ale najpierw należy urządzenia do testowania. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

1. Tworzenie Centrum IoT
2. Zarejestruj urządzenia IoT
3. Uruchom środowisko uruchomieniowe krawędzi IoT
4. Wdrażanie modułu

![Architektura samouczka][2]

Symulowane urządzenie, które możesz utworzyć w tym samouczku jest monitorem na turbiny knie generujący temperatury, wilgotności i wykorzystania danych. Interesują Cię te dane, ponieważ Twoje turbin są wykonywane na różne poziomy wydajności w zależności od pogodą. Innych samouczków usługi Azure IoT krawędzi zależą od pracy, w tym miejscu poprzez wdrożenie modułów, które analiz biznesowych. 

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek zakłada, że używasz komputera lub maszyny wirtualnej z systemem Windows do symulacji urządzenia Internetu rzeczy. 

>[!TIP]
>Jeśli korzystasz z systemu Windows na maszynie wirtualnej, należy włączyć [zagnieżdżonych wirtualizacji] [ lnk-nested] i przydziel co najmniej 2 GB pamięci RAM. 

1. Upewnij się, że używasz wersji systemu Windows:
   * Windows 10 
   * Windows Server
2. Zainstaluj [Docker dla systemu Windows] [ lnk-docker] i upewnij się, że jest uruchomiona.
3. Zainstaluj [Python w systemie Windows] [ lnk-python] i upewnij się, możesz użyć polecenia pip. W tym samouczku przetestowano Python wersji > = 2.7.9 i > = 3.5.4.  
4. Uruchom następujące polecenie, aby pobrać skryptu kontroli IoT krawędzi.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Kontenery systemu Windows lub Linux kontenerów, można uruchomić Azure IoT krawędzi. Jeśli korzystasz z jedną z następujących wersji systemu Windows, możesz użyć kontenery systemu Windows:
>    * Aktualizacja spadek twórców systemu Windows 10
>    * Windows Server 1709 (kompilacja 16299)
>    * Windows IoT Core (16299 kompilacji) na urządzenia z systemem x64
>
> Dla systemu Windows IoT Core, postępuj zgodnie z instrukcjami [zainstaluj środowisko uruchomieniowe IoT Edge w systemie Windows IoT Core][lnk-install-iotcore]. W przeciwnym razie po prostu [skonfigurować Docker na używanie kontenerów Windows][lnk-docker-containers]. Użyj następującego polecenia, aby zweryfikować Twoje wymagania wstępne:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Uruchom samouczek tworzenia Centrum IoT.
![Tworzenie Centrum IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Zarejestruj urządzenia IoT

Zarejestruj urządzenia IoT z nowo utworzonego Centrum IoT.
![Zarejestruj urządzenie][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego krawędzi IoT

Zainstaluj i uruchom środowiska uruchomieniowego Azure IoT Edge na urządzeniu. 
![Zarejestruj urządzenie][5]

Środowisko uruchomieniowe krawędzi IoT jest wdrażana na wszystkich urządzeniach IoT krawędzi. Zawiera dwa moduły. **IoT krawędź agent** ułatwia wdrażanie i monitorowanie modułów na urządzeniu IoT krawędzi. **Centrum IoT krawędzi** zarządza komunikacji między modułami na urządzeniu IoT krawędzi i między urządzeniami a Centrum IoT. Po skonfigurowaniu środowiska uruchomieniowego na nowe urządzenia tylko agent krawędzi IoT rozpocznie się na początku. Centrum IoT krawędzi jest dostarczany później, podczas wdrażania modułu. 


Konfigurowanie środowiska uruchomieniowego z parametrów połączenia urządzenia IoT krawędzi z poprzedniej sekcji.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe.

```cmd
iotedgectl start
```

Sprawdź Docker, aby zobaczyć, czy agent krawędzi IoT jest uruchomiony jako moduł.

```cmd
docker ps
```

![Zobacz edgeAgent w Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzanie urządzeniem krawędzi IoT Azure w chmurze, aby wdrożyć moduł, który będzie wysyłać dane telemetryczne z Centrum IoT.
![Zarejestruj urządzenie][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym samouczku zostało utworzone nowe urządzenie brzegowe IoT i na nim zainstalowany środowiska uruchomieniowego IoT krawędzi. Następnie użyto portalu Azure do umieszczenia krawędzi IoT modułu do uruchomienia na urządzeniu bez konieczności zmiany na urządzeniu. W takim przypadku moduł, który zostanie przypisany tworzy dane środowiska, używanego programu samouczków. 

Otwórz wiersz polecenia na komputerze z uruchomionym symulowane urządzenie ponownie. Upewnij się, że moduł wdrożonych w chmurze jest uruchomiona na urządzeniu IoT krawędzi. 

```cmd
docker ps
```

![Wyświetl trzech modułów na urządzeniu](./media/tutorial-simulate-device-windows/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury. 

```cmd
docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-windows/docker-logs.png)

Można również wyświetlić dane telemetryczne, wysyła urządzenia przy użyciu [narzędzia explorer Centrum IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony nowe urządzenie brzegowe IoT i interfejs chmury Azure IoT krawędzi można wdrożyć kod na urządzeniu. Pojawiły się symulowane urządzenie generowania nieprzetworzone dane o jego środowisku. 

W tym samouczku jest wstępnym dla wszystkich innych samouczków IoT krawędzi. Można kontynuować do wszystkich innych samouczków, aby dowiedzieć się, jak Azure IoT krawędzi można przekształcić danych biznesowych na krawędzi.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie kodu C# jako moduł](tutorial-csharp-module.md)

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