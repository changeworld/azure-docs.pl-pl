---
title: Symulacja usługi Azure IoT Edge w systemie Windows | Microsoft Docs
description: Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge na urządzeniu symulowanym w systemie Windows i wdrażanie pierwszego modułu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bde54a65160c58d8bfba2f6c4c3b6a4317e46ed
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38540235"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Szybki start: wdrażanie pierwszego modułu IoT Edge z witryny Azure Portal na urządzeniu z systemem Windows — wersja zapoznawcza

Usługa Azure IoT Edge umożliwia wykonywanie analiz i przetwarzanie danych na urządzeniach, bez konieczności wypychania wszystkich danych do chmury. W samouczkach usługi IoT Edge przedstawiono sposób wdrażania różnych typów modułów, ale najpierw potrzebne jest urządzenie do ich testowania. 

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie centrum IoT Hub
2. Rejestrowanie urządzenia usługi IoT Edge
3. Uruchamianie środowiska uruchomieniowego usługi IoT Edge
4. Wdrażanie modułu

![Architektura samouczka][2]

Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe. 

>[!NOTE]
>Środowisko uruchomieniowe usługi IoT Edge w systemie Windows jest dostępne w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][lnk-account].

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start założono, że używasz komputera lub maszyny wirtualnej z uruchomionym systemem Windows do symulacji urządzenia IoT. Jeśli korzystasz z systemu Windows na maszynie wirtualnej, włącz [zagnieżdżoną wirtualizację][lnk-nested] i przydziel co najmniej 2 GB pamięci. 

Na maszynie używanej z urządzeniem usługi IoT Edge zapewnij spełnienie następujących wymagań wstępnych:

1. Upewnij się, że używasz obsługiwanej wersji systemu Windows:
   * Windows 10 lub nowszy
   * Windows Server 2016 lub nowszy
2. Zainstaluj aplikację [Docker for Windows][lnk-docker] i upewnij się, że jest uruchomiona.
3. Skonfiguruj platformę Docker do korzystania z [kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start, tworząc centrum IoT Hub w witrynie Azure Portal.
![Tworzenie centrum IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT Hub.
![Rejestrowanie urządzenia][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

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
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
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

  Ustaw tę zmienną środowiskową jako trwałą, aby była zachowywana po ponownym uruchomieniu.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. W pliku `config.yaml` znajdź sekcję **Ustawienia połączenia**. Zaktualizuj wartości **management_uri** i **workload_uri** przy użyciu adresu IP i portów otwartych w poprzedniej sekcji. Zastąp element **\<GATEWAY_ADDRESS\>** swoim adresem IP. 

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

8. Znajdź sekcję **Ustawienia środowiska uruchomieniowego kontenera Moby** i sprawdź, czy wartość **network** została ustawiona na `nat`.

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
    sort-object @{Expression="TimeCreated";Descending=$false}
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

Skonfigurowane w tym przewodniku Szybki start urządzenie symulowane może służyć do testowania samouczków dotyczących usługi IoT Edge. Jeśli chcesz, aby moduł tempSensor przestał wysyłać dane do centrum IoT Hub, użyj poniższego polecenia w celu zatrzymania usługi IoT Edge i usunięcia kontenerów utworzonych na urządzeniu. Jeśli chcesz ponownie użyć maszyny jako urządzenia usługi IoT Edge, pamiętaj o uruchomieniu usługi. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Jeśli nie potrzebujesz już utworzonej przez siebie usługi IoT Hub, możesz użyć polecenia witryny Azure Portal, aby usunąć zasób oraz wszystkie skojarzone z nim urządzenia. Przejdź do strony omówienia centrum IoT Hub i wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia. 

Wszystko jest gotowe, aby kontynuować pracę, korzystając ze wszystkich innych samouczków, i dowiedzieć, jak usługa Azure IoT Edge może ułatwiać przekształcanie tych danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Filtrowanie danych czujnika przy użyciu funkcji platformy Azure](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
