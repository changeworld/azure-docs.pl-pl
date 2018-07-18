---
title: Symulacja usługi Azure IoT Edge w systemie Linux | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak zdalnie wdrożyć wstępnie skompilowany kod na urządzeniu z usługą IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8ee43a1e3b448faae79a7e3086e2e1d639c341f2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611931"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Szybki start: wdrażanie pierwszego modułu usługi IoT Edge na urządzeniu z systemem Linux x64

Usługa Azure IoT Edge umożliwia wykonywanie analiz i przetwarzanie danych na urządzeniach, bez konieczności wypychania wszystkich danych do chmury. W samouczkach usługi IoT Edge przedstawiono sposób wdrażania różnych typów modułów, ale najpierw potrzebne jest urządzenie do ich testowania. 

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub.
2. Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
3. Uruchamianie środowiska uruchomieniowego usługi IoT Edge.
4. Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Architektura samouczka][2]

W tym przewodniku Szybki start komputer lub maszyna wirtualna z systemem Linux zmieni się w urządzenie usługi IoT Edge. Następnie wdrożysz modułu z witryny Azure Portal na swoim urządzeniu. Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe. 

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][lnk-account].

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start będziesz używać maszyny z systemem Linux jako urządzenia usługi IoT Edge. Jeśli nie masz dostępnej maszyny na potrzeby testowania, wykonaj instrukcje przedstawione w artykule [Tworzenie maszyny wirtualnej z systemem Linux w witrynie Azure Portal](../virtual-machines/linux/quick-create-portal.md). 
* Nie musisz wykonywać instrukcji dotyczących instalowania i uruchamiania serwera internetowego. Możesz zakończyć po nawiązaniu połączenia z maszyną wirtualną.  
* Utwórz maszynę wirtualną w nowej grupie zasobów, której możesz użyć do utworzenia pozostałych zasobów platformy Azure wymaganych podczas pracy z tym przewodnikiem Szybki start. Nadaj mu łatwą do rozpoznania nazwę, na przykład *IoTEdgeResources*. 
* Do testowania usługi IoT Edge nie potrzebujesz bardzo dużej maszyny wirtualnej. Wystarczy rozmiar **B1ms**. 

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Rozpocznij pracę z przewodnikiem Szybki start, tworząc centrum IoT Hub w witrynie Azure Portal.
![Tworzenie centrum IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT Hub.
![Rejestrowanie urządzenia][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu. 
![Rejestrowanie urządzenia][5]

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demon zabezpieczeń usługi IoT Edge** jest uruchamiany przy każdym uruchomieniu urządzenia Edge przez rozpoczęciu działania agenta usługi IoT Edge. Agent usługi **IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. 

Wykonaj poniższe czynności na maszynie lub maszynie wirtualnej z systemem Linux przygotowanej na potrzeby tego przewodnika Szybki start. 

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

Zainstaluj środowisko uruchomieniowe kontenera **Moby**.

   ```bash
   sudo apt-get install moby-engine
   ```

Zainstaluj polecenia interfejsu wiersza polecenia dla środowiska Moby. 

   ```bash
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

3. Dodaj parametry połączenia urządzenia usługi IoT Edge. Znajdź zmienną **device_connection_string** i zmień jej wartość na ciąg skopiowany po zarejestrowaniu urządzenia.

4. Zapisz i zamknij plik. 

   `CTRL + X`, `Y`, `Enter`

4. Uruchom ponownie demona zabezpieczeń usługi IoT Edge.

   ```bash
   sudo systemctl restart iotedge
   ```

5. Sprawdź, czy demon zabezpieczeń Edge działa jako usługa systemowa.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Sprawdzanie, czy demon zabezpieczeń Edge został uruchomiony jako usługa systemowa](./media/quickstart-linux/iotedged-running.png)

   Dzienniki demona zabezpieczeń Edge można również wyświetlić, uruchamiając następujące polecenie:

   ```bash
   journalctl -u iotedge
   ```

6. Wyświetl moduły uruchomione na urządzeniu. 

   >[!TIP]
   >Aby móc uruchamiać polecenia `iotedge`, należy najpierw użyć polecenia *sudo*. Wyloguj się na maszynie i zaloguj się ponownie, aby zaktualizować uprawnienia — wówczas będzie możliwe uruchamianie poleceń `iotedge` bez podwyższonego poziomu uprawnień. 

   ```bash
   sudo iotedge list
   ```

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

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. Jeśli nie, możesz usunąć utworzone zasoby platformy Azure oraz usunąć z urządzenia środowisko uruchomieniowe usługi IoT Edge. 

### <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Jeśli grupa zasobów zawiera jakiekolwiek zasoby, które chcesz zachować, po prostu usuń poszczególne niepotrzebne zasoby. 

Aby usunąć grupę zasobów, wykonaj następujące czynności: 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.
2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej usługę IoT Hub. 
3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.
4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

### <a name="remove-the-iot-edge-runtime"></a>Usuwanie środowiska uruchomieniowego usługi IoT Edge

Jeśli chcesz usunąć instalacje z urządzenia, użyj poniższych poleceń.  

Usuń środowisko uruchomieniowe usługi IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Po usunięciu środowiska uruchomieniowego usługi IoT Edge utworzone przez nie kontenery zostaną zatrzymane, ale pozostaną na urządzeniu. Wyświetl wszystkie kontenery.

   ```bash
   sudo docker ps -a
   ```

Usuń kontenery utworzone na urządzeniu przez środowisko uruchomieniowe usługi IoT Edge. Zmień nazwę kontenera tempSensor, jeśli została użyta inna nazwa. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Usuń środowisko uruchomieniowe kontenera.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie symulowane generujące dane pierwotne dotyczące jego otoczenia. 

Wykonanie czynności przedstawionych w tym przewodniku Szybki start jest wymagane do pracy z wszystkimi samouczkami dotyczącymi usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z innych samouczków, aby dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Filtrowanie danych czujnika przy użyciu funkcji platformy Azure](tutorial-deploy-function.md)


<!-- Images -->
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
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
