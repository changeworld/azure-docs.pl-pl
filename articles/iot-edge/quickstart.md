---
title: 'Szybki start: usługa Azure IoT Edge + system Windows | Microsoft Docs'
description: Wypróbuj usługę Azure IoT Edge, uruchamiając analizy na symulowanym urządzeniu Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1437c3552a7af5d5474cf3bdaabe95d5415af603
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414215"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Szybki start: wdrażanie pierwszego modułu IoT Edge z witryny Azure Portal do urządzenia z systemem Windows — wersja zapoznawcza

W tym przewodniku Szybki start użyjesz interfejsu chmury usługi Azure IoT Edge do zdalnego wdrożenia wstępnie utworzonego kodu na urządzeniu z usługą IoT Edge. Aby wykonać to zadanie, użyj najpierw urządzenia z systemem Windows do symulacji urządzenia usługi IoT Edge, co pozwoli wdrożyć na nim moduł.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub.
2. Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
3. Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge na urządzeniu.
4. Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge i wysyłanie telemetrii do usługi IoT Hub.

![Architektura samouczka][2]

Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe. 

>[!NOTE]
>Środowisko uruchomieniowe usługi IoT Edge w systemie Windows jest dostępne w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][lnk-account].

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
   az group create --name IoTEdgeResources --location westus
   ```

Komputer lub maszyna wirtualna z systemem Windows, która będzie działać jako urządzenie usługi IoT Edge: 

* Użyj obsługiwanej wersji systemu Windows:
   * Windows 10 lub nowszy
   * Windows Server 2016 lub nowszy
* Jeśli jest to maszyna wirtualna, włącz [zagnieżdżoną wirtualizację][lnk-nested] i przydziel co najmniej 2 GB pamięci. 
* Zainstaluj aplikację [Docker for Windows][lnk-docker] i upewnij się, że jest uruchomiona.
* Skonfiguruj platformę Docker do korzystania z [kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start, tworząc centrum IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure. 

![Tworzenie centrum IoT Hub][3]

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości używano usługi IoT Hub i masz już utworzone bezpłatne centrum, możesz używać tego centrum IoT Hub. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT Hub. 

Poniższy kod tworzy bezpłatne centrum **F1** w grupie zasobów **IoTEdgeResources**. Zastąp nazwę *{hub_name}* unikatową nazwą centrum IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Jeśli wystąpi błąd, ponieważ w subskrypcji jest już jedno bezpłatne centrum, zmień jednostkę SKU na **S1**. 

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

Zainstaluj i uruchom środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu usługi IoT Edge. 
![Rejestrowanie urządzenia][5]

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demon zabezpieczeń usługi IoT Edge** jest uruchamiany przy każdym uruchomieniu urządzenia Edge przez rozpoczęciu działania agenta usługi IoT Edge. Agent usługi **IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub. 

>[!NOTE]
>Kroki instalacji w tej sekcji są obecnie wykonywane ręcznie — trwa praca nad przygotowaniem skryptu instalacji. 

Instrukcje w tej sekcji służą do konfigurowania środowiska uruchomieniowego usługi IoT Edge przy użyciu kontenerów systemu Linux. Jeśli chcesz używać kontenerów systemu Windows, zobacz [Install Azure IoT Edge runtime on Windows to use with Windows containers (Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Windows do użycia z kontenerami systemu Windows)](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Pobieranie i instalowanie usługi IoT Edge

1. Na urządzeniu usługi IoT Edge uruchom program PowerShell jako administrator.

2. Pobierz pakiet usługi IoT Edge.

   ```powershell
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. Zainstaluj środowisko vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Utwórz i uruchom usługę IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Dodaj wyjątki zapory dla portów używanych przez usługę IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Utwórz nowy plik o nazwie **iotedge.reg** i otwórz go w edytorze tekstów. 

7. Dodaj poniższą zawartość i zapisz plik. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Przejdź do pliku w Eksploratorze plików, a następnie kliknij go dwukrotnie, aby zaimportować zmiany do rejestru systemu Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge 

Skonfiguruj środowisko uruchomieniowe przy użyciu parametrów połączenia urządzenia usługi IoT Edge skopiowanych podczas rejestrowania urządzenia. Skonfiguruj sieć środowiska uruchomieniowego. 

1. Otwórz plik konfiguracji usługi IoT Edge w następującej lokalizacji `C:\ProgramData\iotedge\config.yaml`. Ten plik jest chroniony, dlatego uruchom edytora tekstów, takiego jak Notatnik, jako administrator, a następnie otwórz plik za pomocą edytora. 

2. Znajdź sekcję **aprowizowania** i zaktualizuj wartość elementu **device_connection_string** przy użyciu parametrów skopiowanych ze szczegółów urządzenia usługi IoT Edge. 

3. W oknie programu PowerShell administratora pobierz nazwę hosta dla urządzenia usługi IoT Edge i skopiuj dane wyjściowe. 

   ```powershell
   hostname
   ```

4. W pliku konfiguracji znajdź sekcję **Nazwa hosta urządzenia Edge**. Zaktualizuj wartość **hostname** przy użyciu nazwy hosta skopiowanej z programu PowerShell.

3. W oknie programu PowerShell administratora pobierz adres IP urządzenia usługi IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Skopiuj wartość pola **Adres IPv4** w sekcji **vEthernet (DockerNAT)** danych wyjściowych. 

5. Utwórz zmienną środowiskową o nazwie **IOTEDGE_HOST**, zastępując element *\<ip_address\>* adresem IP urządzenia usługi IoT Edge. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

6. W pliku `config.yaml` znajdź sekcję **Ustawienia połączenia**. Zaktualizuj wartości **management_uri** i **workload_uri** przy użyciu adresu IP i portów otwartych w poprzedniej sekcji. Zastąp element **\<GATEWAY_ADDRESS\>** skopiowanym adresem IP DockerNAT. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Znajdź sekcję **Ustawienia nasłuchiwania** i dodaj te same wartości elementów **management_uri** i **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Znajdź sekcję **Ustawienia środowiska uruchomieniowego kontenera Moby** i sprawdź, czy wartość **network** została ustawiona na **azure-iot-edge**.

   ```yaml
   moby_runtime:
     docker_uri: "npipe://./pipe/docker_engine"
     network: "azure-iot-edge"
   ```

9. Zapisz plik konfiguracji. 

10. W programie PowerShell uruchom ponownie usługę IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane.

1. Sprawdź stan usługi IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest uruchamiany domyślnie i pomaga w instalowaniu i uruchamianiu dodatkowych modułów wdrażanych na urządzeniu. 

   ```powershell
   iotedge list
   ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Rejestrowanie urządzenia][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wypchnięcia modułu usługi IoT Edge do uruchomienia na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu. W tym przypadku wypchnięty moduł tworzy dane środowiskowe, których można użyć na potrzeby samouczków. 

Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge. 

```powershell
iotedge list
```

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu tempSensor do chmury. 

```powershell
iotedge logs tempSensor -f
```

  ![Wyświetlanie danych z modułu](./media/quickstart/iotedge-logs.png)

Możesz również wyświetlić komunikaty odbierane przez centrum IoT Hub przy użyciu [narzędzia eksploratora usługi IoT Hub][lnk-iothub-explorer] lub [rozszerzenia zestawu narzędzi usługi Azure IoT dla edytora Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. Jeśli nie, możesz usunąć utworzone zasoby platformy Azure oraz usunąć z urządzenia środowisko uruchomieniowe usługi IoT Edge. 

### <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Jeśli grupa zasobów zawiera jakiekolwiek zasoby, które chcesz zachować, po prostu usuń poszczególne niepotrzebne zasoby. 

Usuń grupę **IoTEdgeResources**. 

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Usuwanie środowiska uruchomieniowego usługi IoT Edge

Jeśli planujesz korzystanie z urządzenia usługi IoT Edge na potrzeby przyszłych testów, ale chcesz, aby moduł tempSensor przestał wysyłać dane do centrum IoT Hub, gdy nie jest używane, użyj poniższego polecenia w celu zatrzymania usługi IoT Edge. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

Gdy wszystko będzie gotowe do ponownego rozpoczęcia testowania, możesz ponownie uruchomić usługę

   ```powershell
   Start-Service iotedge
   ```

Jeśli chcesz usunąć instalacje z urządzenia, użyj poniższych poleceń.  

Usuń środowisko uruchomieniowe usługi IoT Edge.

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

Po usunięciu środowiska uruchomieniowego usługi IoT Edge utworzone przez nie kontenery zostaną zatrzymane, ale pozostaną na urządzeniu. Wyświetl wszystkie kontenery.

   ```powershell
   docker ps -a
   ```

Usuń kontenery utworzone na urządzeniu przez środowisko uruchomieniowe usługi IoT Edge. Zmień nazwę kontenera tempSensor, jeśli została użyta inna nazwa. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```
   
## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia. 

Wszystko jest gotowe, aby kontynuować pracę, korzystając ze wszystkich innych samouczków, i dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Filtrowanie danych czujnika przy użyciu funkcji platformy Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
