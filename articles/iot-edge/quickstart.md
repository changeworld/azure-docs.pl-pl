---
title: 'Szybki start: usługa Azure IoT Edge + system Windows | Microsoft Docs'
description: Wypróbuj usługę Azure IoT Edge, uruchamiając analizy na symulowanym urządzeniu Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2fd16ab4ade61b1a08f93294051f4246e47839b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631738"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Szybki start: wdrażanie pierwszego modułu IoT Edge z witryny Azure Portal do urządzenia z systemem Windows — wersja zapoznawcza

W tym przewodniku Szybki start użyjesz interfejsu chmury usługi Azure IoT Edge do zdalnego wdrożenia wstępnie utworzonego kodu na urządzeniu z usługą IoT Edge. Aby wykonać to zadanie, użyj najpierw urządzenia z systemem Windows do symulacji urządzenia z usługą IoT Edge, co pozwoli wdrożyć na nim moduł.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][lnk-account].

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że używasz komputera lub maszyny wirtualnej z uruchomionym systemem Windows do symulacji urządzenia Internetu rzeczy. Jeśli korzystasz z systemu Windows na maszynie wirtualnej, włącz [zagnieżdżoną wirtualizację][lnk-nested] i przydziel co najmniej 2 GB pamięci. 

1. Upewnij się, że używasz obsługiwanej wersji systemu Windows:
   * Windows 10 
   * Windows Server
2. Zainstaluj aplikację [Docker for Windows][lnk-docker] i upewnij się, że jest uruchomiona.
3. Zainstaluj środowisko [Python w systemie Windows][lnk-python] i upewnij się, że możesz użyć polecenia pip. Ten przewodnik Szybki start przetestowano przy użyciu wersji środowiska Python >=2.7.9 i >=3.5.4.  
4. Uruchom następujące polecenie, aby pobrać skrypt kontroli usługi IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Usługa Azure IoT Edge może uruchamiać kontenery systemów Windows lub Linux. Aby użyć kontenerów systemu Windows, należy uruchomić:
>    * Windows 10 Fall Creators Update lub
>    * Windows Server 1709 (kompilacja 16299) lub
>    * Windows IoT Core (kompilacja 16299) na urządzeniu z systemem x64
>
> W przypadku systemu Windows IoT Core postępuj zgodnie z instrukcjami w temacie [Zainstaluj środowisko uruchomieniowe usługi IoT Edge w systemie Windows IoT Core][lnk-install-iotcore]. W pozostałych przypadkach wystarczy [skonfigurować program Docker do korzystania z kontenerów systemu Windows][lnk-docker-containers] i opcjonalnie zweryfikować wymagania wstępne przy użyciu następującego polecenia programu Powershell:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Tworzenie centrum IoT za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz centrum IoT w subskrypcji platformy Azure. W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli usługa IoT Hub była używana w przeszłości i bezpłatne centrum zostało już utworzone, możesz pominąć tę sekcję i przejść do tematu [Rejestrowanie urządzenia IoT Edge][anchor-register]. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT. 

1. Zaloguj się w witrynie [Azure Portal][lnk-portal]. 
1. Wybierz przycisk **Cloud Shell**. 

   ![Przycisk Cloud Shell][1]

1. Utwórz grupę zasobów. Poniższy kod tworzy grupę zasobów o nazwie **IoTEdge** w regionie **Zachodnich stanów USA**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Utwórz centrum IoT w nowej grupie zasobów. Poniższy kod tworzy bezpłatne centrum **F1** o nazwie **MyIotHub** w grupie zasobów **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Rejestracja urządzenia usługi IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Zawiera dwa moduły. Po pierwsze agent usługi IoT Edge ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge. Po drugie centrum usługi IoT Edge zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. 

Skonfiguruj środowisko uruchomieniowe za pomocą ciągu połączenia urządzenia usługi IoT Edge z poprzedniej sekcji.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Uruchom środowisko uruchomieniowe.

```cmd
iotedgectl start
```

Sprawdź aplikację Docker, aby zobaczyć, że agent usługi IoT Edge jest uruchomiony jako moduł.

```cmd
docker ps
```

![Zobacz agenta usługi Edge w aplikacji Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć do samouczków. 

Otwórz ponownie wiersz polecenia na komputerze z uruchomionym symulowanym urządzeniem. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge. 

```cmd
docker ps
```

![Wyświetlanie trzech modułów na urządzeniu](./media/tutorial-simulate-device-windows/docker-ps2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury. 

```cmd
docker logs -f tempSensor
```

![Wyświetlanie danych z modułu](./media/tutorial-simulate-device-windows/docker-logs.png)

Możesz też wyświetlić telemetrię wysyłaną przez urządzenie przy użyciu [narzędzia eksploratora usługi IoT Hub][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć utworzone przez siebie symulowane urządzenie, razem z kontenerami aplikacji Docker, które zostały uruchomione dla każdego modułu, użyj następującego polecenia: 

```cmd
iotedgectl uninstall
```

Jeżeli nie potrzebujesz już utworzonej przez siebie usługi IoT Hub, możesz użyć polecenia [az iot hub delete][lnk-delete], aby usunąć zasób oraz wszystkie powiązane z nim urządzenia:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Następne kroki

Wiesz, jak wdrożyć moduł usługi IoT Edge na urządzeniu usługi IoT Edge. Teraz spróbuj wdrożyć różne typy usług Azure jako moduły, dzięki czemu będzie można analizować dane na krawędzi. 

* [Wdrażanie usługi Azure Functions jako modułu](tutorial-deploy-function.md)
* [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)
* [Wdrażanie własnego kodu jako modułu](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
