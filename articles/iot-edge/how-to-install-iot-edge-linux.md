---
title: Jak zainstalować usługi Azure IoT Edge w systemie Linux | Dokumentacja firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge w systemie Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: kgremban
ms.openlocfilehash: be8fb801acfb625685604a302051d813bfd97939
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325306"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64)

Środowisko uruchomieniowe usługi Azure IoT Edge jest wdrażana na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta usługi IoT Edge. **Agenta usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi Edge, w tym Centrum IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge w swojej x64 (Intel/AMD) w systemie Linux urządzenia Edge.

>[!NOTE]
>Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Zarejestruj kanał repozytorium klucza i oprogramowania firmy Microsoft

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

Usługa Azure IoT Edge opiera się na [zgodnego z OCI] [ lnk-oci] kontener środowiska uruchomieniowego. Na potrzeby scenariuszy produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby] [ lnk-moby] aparatu przedstawione poniżej. Jest aparat tylko kontenera oficjalnie obsługiwana dzięki usłudze Azure IoT Edge. Obrazy usługi docker CE/EE kontenerów są zgodne ze środowiskiem uruchomieniowym Moby.

Aktualizacja polecenia apt-get.

```bash
sudo apt-get update
```

Zainstaluj aparat Moby. 

```bash
sudo apt-get install moby-engine
```

Zainstaluj Moby interfejsu wiersza polecenia (CLI). Interfejs wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

Poniższe polecenia zostanie również zainstalowana standardowa wersja **iothsmlib** Jeśli nie są już zainstalowane.

```bash
sudo apt-get update
sudo apt-get install iotedge
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

## <a name="next-steps"></a>Kolejne kroki

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z] [ lnk-trouble] strony.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
