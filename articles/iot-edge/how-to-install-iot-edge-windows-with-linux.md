---
title: Jak zainstalować krawędzi IoT Azure w systemie Windows z kontenerów systemu Linux | Dokumentacja firmy Microsoft
description: Usługa Azure IoT krawędzi instrukcje dotyczące instalacji w systemie Windows z kontenerami systemu Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036003"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Zainstalować środowisko uruchomieniowe krawędzi IoT Azure w systemie Windows do użycia z kontenerami systemu Linux

Środowisko uruchomieniowe Azure IoT krawędzi jest wdrażana na wszystkich urządzeniach IoT krawędzi. Składa się z trzech składników. **Krawędzi IoT zabezpieczeń demon** zapewnia i przechowuje standardów zabezpieczeń na urządzeniu krawędzi. Demon rozpoczyna się na każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta IoT krawędzi. **IoT krawędź agent** ułatwia wdrażanie i monitorowanie modułów na urządzeniu krawędzi, w tym Centrum IoT krawędzi. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe Azure IoT Edge na x64 (AMD/Intel) systemu Windows systemu. Obsługa systemu Windows jest obecnie w przeglądzie.

>[!NOTE]
Używanie kontenerów systemu Linux na ows Windows konfiguracja nie jest produkcyjnych zalecane lub obsługiwanych Azure IoT Edge. Jednak może służyć do tworzenia i testowania.

## <a name="supported-windows-versions"></a>Obsługiwane wersje systemu Windows
Azure IoT krawędzi może być używany do tworzenia i testowania w następujących wersjach systemu Windows, korzystając z kontenerów Linux:
  * Windows 10 lub nowsze systemy operacyjne.
  * Windows Server 2016 lub nowych systemów operacyjnych serwera.

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

Zależy od usługi Azure IoT krawędzi [OCI zgodnego] [ lnk-oci] kontener środowiska uruchomieniowego (np. Docker). 

Można użyć [Docker dla systemu Windows] [ lnk-docker-for-windows] do projektowania i testowania. Upewnij się, Docker dla systemu Windows jest [skonfigurowana do używania kontenery systemu Linux][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń krawędzi IoT Azure

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT krawędzi obowiązują postanowienia licencyjne, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoje instalacji i używania pakietu oznacza akceptację postanowień niniejszych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie należy używać pakietu.

### <a name="download-the-edge-daemon-package-and-install"></a>Demon krawędzi Pobierz i zainstaluj

W oknie Administrator programu PowerShell uruchom następujące polecenia:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Zainstaluj za pomocą vcruntime:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Tworzenie i uruchamianie *iotedge* usługi:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Dodawanie wyjątków zapory dla portów używanych przez usługę:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Utwórz **iotedge.reg** pliku o następującej zawartości, a następnie zaimportować w rejestrze systemu Windows przez dwukrotne kliknięcie go lub za pomocą `reg import iotedge.reg` polecenia:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Skonfiguruj demona zabezpieczeń krawędzi IoT Azure

Demon można skonfigurować przy użyciu pliku konfiguracji w `C:\ProgramData\iotedge\config.yaml` urządzenie brzegowe można skonfigurować <!--[automatically via Device Provisioning Service][lnk-dps] or--> ręcznie przy użyciu [ciąg połączenia urządzenia][lnk-dcs].

W przypadku ręcznej konfiguracji, wprowadź ciąg połączenia urządzenia w **inicjowania obsługi administracyjnej:** sekcji **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Pobierz nazwę krawędzi urządzeniami przy użyciu `hostname` poleceń w programie PowerShell i ustaw go jako wartość **hostname:** w yaml programu configuration. Na przykład:

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Następnie musisz podać adres ip i portu dla **workload_uri** i **management_uri** w **connect:** sekcji konfiguracji.

Wprowadź adres ip `ipconfig` w oknie programu PowerShell i wybierz adres ip **vEthernet (DockerNAT)**"interface, jak pokazano w poniższym przykładzie (adres ip w systemie mogą być różne):

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Wprowadź tych samych adresów w **nasłuchiwania:** sekcji konfiguracji. Na przykład:

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

W oknie programu PowerShell Utwórz zmienną środowiskową **IOTEDGE_HOST** z **management_uri** adresu, na przykład:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

Ponadto upewnij się, **sieci:** w obszarze **moby_runtime:** jest odkomentowana i **azure iot krawędzi**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Zapisz plik konfiguracji i ponownie uruchom usługę:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Można sprawdzić stanu usługi IoT krawędzi przez: 

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut, za pomocą:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

I uruchomione moduły z listy:

```powershell
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz problemy ze środowiskiem uruchomieniowym krawędzi poprawnie, instalowanie wyewidencjonowania [Rozwiązywanie problemów z] [ lnk-trouble] strony.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

