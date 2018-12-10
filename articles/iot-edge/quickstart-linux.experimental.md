---
title: 'Szybki start: usługa Azure IoT Edge + system Linux | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak zdalnie wdrożyć wstępnie skompilowany kod na urządzeniu z usługą IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2d988d70285e64414277bc2337a564aa87e45201
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888141"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Szybki start: wdrażanie pierwszego modułu usługi IoT Edge na urządzeniu z systemem Linux x64

Usługa Azure IoT Edge przenosi możliwości chmury na urządzenia Internetu rzeczy. Z tego przewodnika Szybki start dowiesz się, jak używać interfejsu do zdalnego wdrażania wstępnie skompilowanego kodu na urządzeniu z usługą IoT Edge.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub.
2. Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
3. Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge na urządzeniu.
4. Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Architektura przewodnika Szybki start](./media/quickstart-linux/install-edge-full.png)

W tym przewodniku Szybki start komputer lub maszyna wirtualna z systemem Linux zmieni się w urządzenie usługi IoT Edge. Następnie wdrożysz modułu z witryny Azure Portal na swoim urządzeniu. Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas wykonywania wielu kroków tego przewodnika Szybki start jest używany interfejs wiersza polecenia platformy Azure, a usługa Azure IoT ma rozszerzenie umożliwiające włączenie dodatkowych funkcji. 

Dodaj rozszerzenie usługi Azure IoT do wystąpienia usługi Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Wymagania wstępne

Zasoby w chmurze: 

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Urządzenie usługi IoT Edge:

* Urządzenie lub maszyna wirtualna z systemem Linux, która będzie działać jako urządzenie usługi IoT Edge. Zaleca się użycie maszyny wirtualnej z [usługą Azure IoT Edge w systemie Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) dostarczanej przez firmę Microsoft, która preinstaluje środowisko uruchomieniowe usługi IoT Edge. Utwórz tę maszynę wirtualną za pomocą następującego polecenia:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Podczas tworzenia nowej maszyny wirtualnej zanotuj wartość elementu **publicIpAddress**. Jest ona widoczna w danych wyjściowych polecenia create. Tego publicznego adresu IP użyjesz później w tym przewodniku Szybki start do nawiązania połączenia z maszyną wirtualną.

* Jeśli wolisz użyć środowiska uruchomieniowego usługi Azure IoT Edge w systemie lokalnym, postępuj zgodnie z instrukcjami w artykule [Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64)](how-to-install-iot-edge-linux.md).

* Jeśli chcesz użyć urządzenia z architekturą ARM32, takiego jak Raspberry Pi, postępuj zgodnie z instrukcjami w artykule [Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start, tworząc centrum IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure.

![Tworzenie centrum IoT Hub](./media/quickstart-linux/create-iot-hub.png)

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości używano usługi IoT Hub i masz już utworzone bezpłatne centrum, możesz używać tego centrum IoT Hub. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT Hub. 

Poniższy kod tworzy bezpłatne centrum **F1** w grupie zasobów **IoTEdgeResources**. Zastąp nazwę *{hub_name}* unikatową nazwą centrum IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Jeśli wystąpi błąd, ponieważ w subskrypcji jest już jedno bezpłatne centrum, zmień jednostkę SKU na **S1**. Jeśli wystąpi błąd polegający na niedostępności nazwy centrum IoT Hub, oznacza to, że ktoś inny ma już centrum o takiej nazwie. Wypróbuj nową nazwę. 

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.
![Rejestrowanie urządzenia](./media/quickstart-linux/register-device.png)

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Tożsamość urządzenia jest przechowywana w chmurze. Aby skojarzyć urządzenie fizyczne z tożsamością urządzenia, używane są unikatowe parametry połączenia. 

Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, zadeklaruj tę tożsamość jako należącą do urządzenia usługi IoT Edge za pomocą flagi `--edge-enabled`. 

1. W powłoce chmury platformy Azure wprowadź poniższe polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w swoim centrum.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Jeśli wystąpi błąd dotyczący kluczy zasad iothubowner, upewnij się, że usługa Cloud Shell działa, bazując na najnowszej wersji rozszerzenia azure-cli-iot-ext. 

2. Pobierz parametry połączenia danego urządzenia, które łączy urządzenie fizyczne z tożsamością w usłudze IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Skopiuj parametry połączenia i zapisz je. Za pomocą tej wartości skonfigurujesz środowisko uruchomieniowe usługi IoT Edge w następnej sekcji. 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>Łączenie urządzenia usługi IoT Edge z centrum IoT Hub

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu usługi IoT Edge. 
![Rejestrowanie urządzenia](./media/quickstart-linux/start-runtime.png)

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demon zabezpieczeń usługi IoT Edge** jest uruchamiany przy każdym uruchomieniu urządzenia Edge przez rozpoczęciu działania agenta usługi IoT Edge. Agent usługi **IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. 

Podczas konfigurowania środowiska uruchomieniowego należy podać parametry połączenia urządzenia. Użyj parametrów pobranych za pomocą wiersza polecenia platformy Azure. Za pomocą tych parametrów urządzenie fizyczne jest kojarzone z tożsamością urządzenia usługi IoT Edge na platformie Azure. 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Ustawianie parametrów połączenia na urządzeniu usługi IoT Edge

* Jeśli korzystasz z maszyny wirtualnej z usługą Azure IoT Edge w systemie Ubuntu, użyj parametrów połączenia urządzenia skopiowanych wcześniej, aby zdalnie skonfigurować urządzenie usługi IoT Edge:

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   Na potrzeby kolejnych kroków pobierz publiczny adres IP zwrócony przez polecenie tworzenia. Publiczny adres IP możesz również znaleźć na stronie przeglądu swojej maszyny wirtualnej w witrynie Azure Portal. Użyj następującego polecenia, aby nawiązać połączenie z maszyną wirtualną. Zastąp ciąg **{publicIpAddress}** adresem swojej maszyny. 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* Jeśli korzystasz z usługi IoT Edge na maszynie lokalnej lub z urządzenia z architekturą ARM32, otwórz plik konfiguracji /etc/iotedge/config.yaml i zaktualizuj zmienną **device_connection_string** za pomocą wartości skopiowanej wcześniej, a następnie uruchom ponownie demona zabezpieczeń usługi IoT Edge, aby zastosować zmiany:

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Dopóki nie zostanie to zrobione, umieszczaj przedrostek **„sudo”** przed poleceniami. 

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane.

1. Sprawdź, czy demon zabezpieczeń Edge działa jako usługa systemowa.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Sprawdzanie, czy demon zabezpieczeń Edge został uruchomiony jako usługa systemowa](./media/quickstart-linux/iotedged-running.png)

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki. 

   ```bash
   journalctl -u iotedge
   ```

3. Wyświetl moduły uruchomione na urządzeniu. 

   ```bash
   sudo iotedge list
   ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart-linux/iotedge-list-1.png)

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze. 

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Rejestrowanie urządzenia](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć na potrzeby samouczków.

Otwórz ponownie wiersz polecenia na urządzeniu usługi IoT Edge lub użyj połączenia SSH z interfejsu wiersza polecenia platformy Azure. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart-linux/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![Wyświetlanie danych z modułu](./media/quickstart-linux/iotedge-logs.png)

Moduł czujnika temperatury może oczekiwać na połączenie z centrum funkcji Edge, jeśli ostatni wiersz wyświetlony w dzienniku to `Using transport Mqtt_Tcp_Only`. Spróbuj wyłączyć moduł i pozwolić agentowi funkcji Edge na jego ponowne uruchomienie. Możesz go zabić przy użyciu polecenia `sudo docker stop tempSensor`.

Możesz również wyświetlić komunikaty odbierane przez centrum IoT przy użyciu [rozszerzenia zestawu narzędzi usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. Jeśli nie, możesz usunąć utworzone zasoby platformy Azure oraz usunąć z urządzenia środowisko uruchomieniowe usługi IoT Edge.

### <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Sprawdź dokładnie zawartość grupy zasobów, aby się upewnić, że nie ma w niej żadnych elementów, które chcesz zachować. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

Usuń grupę **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

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
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Następne kroki

Wykonanie czynności przedstawionych w tym przewodniku Szybki start jest wymagane do pracy z wszystkimi samouczkami dotyczącymi usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Filtrowanie danych czujnika przy użyciu funkcji platformy Azure](tutorial-deploy-function.md)
