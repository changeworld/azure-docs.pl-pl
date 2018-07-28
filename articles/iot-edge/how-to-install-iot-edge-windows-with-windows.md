---
title: Jak zainstalować usługi Azure IoT Edge na Windows za pomocą kontenerów Windows | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje dotyczące instalacji na Windows za pomocą kontenerów Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5fc1163f590b2408fca913e35e57f014424b225c
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308402"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows do korzystania z kontenerów Windows

Środowisko uruchomieniowe usługi Azure IoT Edge jest wdrażana na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta usługi IoT Edge. **Agenta usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi Edge, w tym Centrum IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na usługi Windows x64 (AMD/Intel) systemu. 

Obsługa Windows jest obecnie w wersji zapoznawczej.

## <a name="supported-windows-versions"></a>Obsługiwane wersje Windows
Usługa Azure IoT Edge przy użyciu kontenerów Windows mogą być używane z:
  * Windows 10 Enterprise na IoT/IoT Core z kwietnia 2018 r. Zaktualizuj (Tworzenie 17134).
  * System Windows Server w wersji 1803

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

>[!NOTE]
>W przypadku instalacji aparatu kontenera na Windows IoT Core, wykonaj kroki z [aprowizowanie urządzenia IoT Core artykułu] [ lnk-iot-core] , a następnie kontynuuj z poniższymi instrukcjami.

Usługa Azure IoT Edge opiera się na [zgodnego z OCI] [ lnk-oci] kontener środowiska uruchomieniowego (np. Docker). Możesz użyć [Docker for Windows] [ lnk-docker-for-windows] do tworzenia i testowania. 

**Upewnij się, Docker for Windows [skonfigurowany do używania kontenerów Windows][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

### <a name="download-the-edge-daemon-package-and-install"></a>Pobierz pakiet demonów Edge i zainstaluj

W oknie programu PowerShell administratora uruchom następujące polecenia:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

Zainstaluj vcruntime przy użyciu (możesz pominąć ten krok na urządzeniu usługi IoT Edge podstawowej):

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Tworzenie i uruchamianie **iotedge** usługi:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Dodać wyjątki zapory dla portów używanych przez usługę:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Tworzenie **iotedge.reg** pliku o następującej zawartości i importowanie w rejestrze Windows przez dwukrotne kliknięcie go lub za pomocą `reg import iotedge.reg` polecenia:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Skonfiguruj demona zabezpieczeń usługi Azure IoT Edge

Demon można skonfigurować przy użyciu pliku konfiguracji na `C:\ProgramData\iotedge\config.yaml`.

Urządzenie brzegowe można skonfigurować ręcznie przy użyciu [parametry połączenia urządzenia] [ lnk-dcs] lub [automatycznie za pomocą usługi Device Provisioning] [ lnk-dps].

* Konfiguracja ręczna, usuń znaczniki komentarza **ręczne** tryb obsługi administracyjnej. Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia usługi IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Konfiguracja automatyczna, usuń znaczniki komentarza **dps** tryb obsługi administracyjnej. Zaktualizuj wartości **scope_id** i **registration_id** wartościami z wystąpieniem usługi DPS Centrum IoT i urządzenia usługi IoT Edge przy użyciu modułu TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Pobierz nazwę przy użyciu urządzenia usługi edge `hostname` poleceń w programie PowerShell i ustawić ją jako wartość **hostname:** w yaml konfiguracji. Na przykład:

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

Następnie podaj adres ip i port dla **workload_uri** i **management_uri** w **connect:** i **nasłuchiwania:** sekcje Konfiguracja.

Aby pobrać adres ip, wprowadź `ipconfig` w oknie programu PowerShell i skopiuj adres ip **vEthernet (nat)** interfejsu, jak pokazano w poniższym przykładzie (adres ip w systemie może się różnić):  

![nat][img-nat]

Aktualizacja **workload_uri** i **management_uri** w **connect:** sekcję pliku konfiguracji. Zastąp **\<GATEWAY_ADDRESS\>** o adresie IP vEthernet, który został skopiowany.

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Wprowadź ten sam adresy w **nasłuchiwania:** sekcji.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

W oknie programu PowerShell Utwórz zmienną środowiskową **IOTEDGE_HOST** z **management_uri** adresu.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Ustaw tę zmienną środowiskową jako trwałą, aby była zachowywana po ponownym uruchomieniu.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Na koniec upewnij się, **sieci:** w obszarze **moby_runtime:** jest odkomentowana i **translatora adresów sieciowych**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

Zapisz plik konfiguracji, a następnie uruchom ponownie usługę:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. Jeśli użyto **automatycznej konfiguracji** kroki należy wykonać kilka dodatkowych kroków, środowisko uruchomieniowe można zarejestrować urządzenia za pomocą usługi IoT hub w Twoim imieniu. Dalsze czynności, zobacz [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).

Można sprawdzić stanu usługi IoT Edge przez: 

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi dla ostatnich 5 minut, przy użyciu:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

I uruchomione moduły z listy:

```powershell
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli występują problemy z środowiska uruchomieniowego usługi Edge instalacji prawidłowo, wyewidencjonowania [Rozwiązywanie problemów z] [ lnk-trouble] strony.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md

