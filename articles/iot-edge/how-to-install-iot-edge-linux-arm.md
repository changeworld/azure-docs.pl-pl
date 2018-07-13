---
title: Jak zainstalować usługi Azure IoT Edge w systemie Linux | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje instalacji w systemie Linux na ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5b5212d5e1663fee01ff87642432818071d4f4dd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988538"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)

Środowisko uruchomieniowe usługi Azure IoT Edge jest wdrażana na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta usługi IoT Edge. **Agenta usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi Edge, w tym Centrum IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu Linux ARM32v7/armhf krawędzi (na przykład urządzenia Raspberry Pi).

>[!NOTE]
>Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

Usługa Azure IoT Edge opiera się na [zgodnego z OCI] [ lnk-oci] kontener środowiska uruchomieniowego. Na potrzeby scenariuszy produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby] [ lnk-moby] aparatu przedstawione poniżej. Jest aparat tylko kontenera oficjalnie obsługiwana dzięki usłudze Azure IoT Edge. Obrazy kontenera usługi docker CE/EE są zgodne ze środowiskiem uruchomieniowym na podstawie Moby.

Poniższe polecenia zainstalować zarówno aparat oparty na Moby, jak i interfejs wiersza polecenia (CLI). Interfejs wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi IoT Edge

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Skonfiguruj demona zabezpieczeń usługi Azure IoT Edge


Demon można skonfigurować przy użyciu pliku konfiguracji na `/etc/iotedge/config.yaml`. Plik jest zabezpieczony przed zapisem domyślnie, może być konieczne podwyższonym poziomem uprawnień, aby go edytować.

```bash
sudo nano /etc/iotedge/config.yaml
```

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

Po wprowadzeniu informacji o udostępnianiu w konfiguracji, należy ponownie uruchomić demona:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. Jeśli użyto **automatycznej konfiguracji** kroki należy wykonać kilka dodatkowych kroków, środowisko uruchomieniowe można zarejestrować urządzenia za pomocą usługi IoT hub w Twoim imieniu. Dalsze czynności, zobacz [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na maszynie wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Możesz sprawdzić stan, IoT Edge demona przy użyciu:

```cmd/sh
systemctl status iotedge
```

Sprawdź dzienniki demona przy użyciu:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

I uruchomione moduły z listy:

```cmd/sh
sudo iotedge list
```
>[!NOTE]
>W zasobie ograniczonego urządzeniami, takimi jak RaspberryPi, zdecydowanie zalecane jest, *OptimizeForPerformance* ustawiono zmienną środowiskową *false* zgodnie z instrukcjami w [ Przewodnik rozwiązywania problemów.][lnk-trouble]


## <a name="next-steps"></a>Kolejne kroki

Jeśli występują problemy z środowiska uruchomieniowego usługi Edge instalacji prawidłowo, wyewidencjonowania [Rozwiązywanie problemów z] [ lnk-trouble] strony.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
