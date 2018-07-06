---
title: 'Szybki start: usługa Azure IoT Edge + system Linux | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak zdalnie wdrożyć wstępnie skompilowany kod na urządzeniu z usługą IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 27e5b7fed227248d9d60c8ede460c9ecc65ca52d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096278"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Szybki start: wdrażanie pierwszego modułu usługi IoT Edge na urządzeniu z systemem Linux x64

Usługa Azure IoT Edge przenosi możliwości chmury na urządzenia Internetu rzeczy. Z tego przewodnika Szybki start dowiesz się, jak używać interfejsu do zdalnego wdrażania wstępnie skompilowanego kodu na urządzeniu z usługą IoT Edge.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub.
2. Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
3. Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge na urządzeniu.
4. Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Architektura przewodnika Szybki start][2]

W tym przewodniku Szybki start komputer lub maszyna wirtualna z systemem Linux zmieni się w urządzenie usługi IoT Edge. Następnie wdrożysz modułu z witryny Azure Portal na swoim urządzeniu. Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe. 

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][lnk-account].


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas wykonywania wielu kroków tego przewodnika Szybki start jest używany interfejs wiersza polecenia platformy Azure, a usługa Azure IoT ma rozszerzenie umożliwiające włączenie dodatkowych funkcji. 

Dodaj rozszerzenie usługi Azure IoT do wystąpienia powłoki chmury.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Rozpocznij pracę z przewodnikiem Szybki start, tworząc centrum IoT Hub w witrynie Azure Portal.
![Tworzenie centrum IoT Hub][3]

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości używano usługi IoT Hub i masz już utworzone bezpłatne centrum, możesz używać tego centrum IoT Hub. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT. 

1. W powłoce chmury platformy Azure utwórz grupę zasobów. Poniższy kod tworzy grupę zasobów o nazwie **TestResources** w regionie **Zachodnie stany USA**. Dzięki wprowadzeniu wszystkich zasobów dla przewodników Szybki start i samouczków do grupy można nimi zarządzać jednocześnie. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Utwórz centrum IoT w nowej grupie zasobów. Poniższy kod tworzy bezpłatne centrum **F1** w grupie zasobów **TestResources**. Zastąp nazwę *{hub_name}* unikatową nazwą centrum IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT Hub.
![Rejestrowanie urządzenia][4]

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, już na samym początku zadeklaruj to urządzenie jako urządzenie usługi IoT Edge. 

1. W powłoce chmury platformy Azure wprowadź poniższe polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w swoim centrum.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Pobierz parametry połączenia danego urządzenia, które łączy urządzenie fizyczne z tożsamością w usłudze IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Skopiuj parametry połączenia i zapisz je. Za pomocą tej wartości skonfigurujesz środowisko uruchomieniowe usługi IoT Edge w następnej sekcji. 


## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu. 
![Rejestrowanie urządzenia][5]

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demon zabezpieczeń usługi IoT Edge** jest uruchamiany przy każdym uruchomieniu urządzenia Edge przez rozpoczęciu działania agenta usługi IoT Edge. Agent usługi **IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. 

### <a name="register-your-device-to-use-the-software-repository"></a>Rejestrowanie urządzenia w celu korzystania z repozytorium oprogramowania

Pakiety potrzebne do uruchamiania środowiska uruchomieniowego usługi IoT Edge są zarządzane w repozytorium oprogramowania. Urządzenie usługi IoT Edge należy skonfigurować do uzyskiwania dostępu do tego repozytorium. 

Czynności opisane w tej sekcji dotyczą urządzeń z systemem **Ubuntu 16.04**. Aby uzyskać dostęp do repozytorium oprogramowania w innych wersjach systemu Linux, zobacz [Install the Azure IoT Edge runtime on Linux (x64) (Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64))](how-to-install-iot-edge-linux.md) lub [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf) (Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf))](how-to-install-iot-edge-linux-arm.md).

1. Na maszynie używanej jako urządzenie usługi IoT Edge zainstaluj konfigurację repozytorium.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Zainstaluj klucz publiczny umożliwiający uzyskiwanie dostępu do repozytorium.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instalowanie środowiska uruchomieniowego kontenera

Środowisko uruchomieniowe usługi IoT Edge to jest zestaw kontenerów oraz logika wdrażana na urządzeniu IoT Edge w postaci pakietu kontenerów. Przygotuj urządzenie do użycia tych składników, instalując środowisko uruchomieniowe kontenera.

Zaktualizuj element **apt-get**.

   ```bash
   sudo apt-get update
   ```

Zainstaluj środowisko uruchomieniowe kontenera Moby oraz polecenia jego interfejsu wiersza polecenia. 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Zainstalowanie i konfigurowanie demona zabezpieczeń usługi IoT Edge

Demon zabezpieczeń jest instalowany jako usługa systemowa, aby środowisko uruchomieniowe usługi IoT Edge rozpoczynało działanie po każdym uruchomieniu urządzenia. Instalacja obejmuje również wersję elementu **hsmlib**, która umożliwia demonowi zabezpieczeń interakcję z zabezpieczeniami sprzętowymi urządzenia. 

1. Pobierz i zainstaluj demona zabezpieczeń usługi IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Otwórz plik konfiguracji usługi IoT Edge. Jest to plik chroniony, więc może zajść potrzeba użycia podwyższonych uprawnień w celu uzyskania dostępu do tego pliku.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Dodaj parametry połączenia urządzenia usługi IoT Edge skopiowane podczas rejestrowania urządzenia. Zastąp wartość zmiennej **device_connection_string** skopiowanej we wcześniejszej części tego przewodnika Szybki start.

4. Uruchom ponownie demona zabezpieczeń Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Sprawdź, że demon zabezpieczeń Edge działa jako usługa systemowa:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Sprawdzanie, czy demon zabezpieczeń Edge został uruchomiony jako usługa systemowa](./media/quickstart-linux/iotedged-running.png)

   Dzienniki demona zabezpieczeń Edge można również wyświetlić, uruchamiając następujące polecenie:

   ```bash
   journalctl -u iotedge
   ```

6. Wyświetl moduły uruchomione na urządzeniu: 

   ```bash
   sudo iotedge list
   ```

   Po wylogowaniu i zalogowaniu elementu *sudo* nie trzeba używać w powyższym poleceniu.

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Rejestrowanie urządzenia][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć na potrzeby samouczków. 

Otwórz ponownie wiersz polecenia na komputerze z uruchomionym urządzeniem symulowanym. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge:

   ```bash
   sudo iotedge list
   ```
   Po wylogowaniu i zalogowaniu elementu *sudo* nie trzeba używać w powyższym poleceniu.

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart-linux/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
Po wylogowaniu i zalogowaniu elementu *sudo* nie trzeba używać w powyższym poleceniu.

![Wyświetlanie danych z modułu](./media/quickstart-linux/iotedge-logs.png)

Moduł czujnika temperatury może oczekiwać na połączenie z centrum funkcji Edge, jeśli ostatni wiersz wyświetlony w dzienniku to `Using transport Mqtt_Tcp_Only`. Spróbuj wyłączyć moduł i pozwolić agentowi funkcji Edge na jego ponowne uruchomienie. Możesz go zabić przy użyciu polecenia `sudo docker stop tempSensor`.

Możesz również wyświetlić dane telemetryczne wysyłane przez urządzenie przy użyciu [narzędzia eksploratora usługi IoT Hub][lnk-iothub-explorer] lub [rozszerzenia zestawu narzędzi usługi Azure IoT dla edytora Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. Jeśli chcesz usunąć instalacje z urządzenia, użyj poniższych poleceń.  

Usuń środowisko uruchomieniowe usługi IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Usuń kontenery utworzone na urządzeniu. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Usuń środowisko uruchomieniowe kontenera.

   ```bash
   sudo apt-get remove --purge moby
   ```

Jeśli nie ma potrzebujesz już utworzonych zasobów platformy Azure, możesz użyć poniższego polecenia do usunięcia utworzonej grupę zasobów i wszystkich skojarzonych z nią zasobów:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Następne kroki

Wykonanie czynności przedstawionych w tym przewodniku Szybki start jest wymagane do pracy z wszystkimi samouczkami dotyczącymi usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Filtrowanie danych czujnika przy użyciu funkcji platformy Azure](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
