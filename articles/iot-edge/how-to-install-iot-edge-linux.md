---
title: Jak zainstalować usługi Azure IoT Edge w systemie Linux | Dokumentacja firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge w systemie Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 56223b2ed8e9d9b1a08f5313940920113a650bfe
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128336"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64)

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge w swojej x64 (Intel/AMD) w systemie Linux urządzenia Edge. Zapoznaj się [pomocy technicznej usługi Azure IoT Edge](support.md#operating-systems) listę systemów operacyjnych AMD64, które są obecnie obsługiwane. 

>[!NOTE]
>Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Zarejestruj kanał repozytorium klucza i oprogramowania firmy Microsoft

W zależności od systemu operacyjnego należy wybrać odpowiednie skrypty w celu przygotowania urządzenia IoT Edge instalacji środowiska uruchomieniowego. 

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
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

**Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie pozostałych środowiska uruchomieniowego usługi IoT Edge. 

Polecenie instalacji instaluje również standardowej wersji **iothsmlib** Jeśli nie są już zainstalowane.

Aktualizacja polecenia apt-get.

```bash
sudo apt-get update
```

Instalowanie demona zabezpieczeń. Pakiet jest zainstalowany na `/etc/iotedge/`.

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Skonfiguruj demona zabezpieczeń usługi Azure IoT Edge

Konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby połączyć urządzenia fizycznego przy użyciu tożsamości urządzenia, która znajduje się w usłudze Azure IoT hub. 

Demon można skonfigurować przy użyciu pliku konfiguracji na `/etc/iotedge/config.yaml`. Plik jest zabezpieczony przed zapisem domyślnie, może być konieczne podwyższonym poziomem uprawnień, aby go edytować.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

### <a name="option-1-manual-provisioning"></a>Opcja 1: Aprowizacja ręczna

Aby ręcznie zainicjować obsługę urządzeń, należy podać go [parametry połączenia urządzenia] [ lnk-dcs] , możesz utworzyć przez zarejestrowanie nowego urządzenia w usłudze IoT hub.


Otwórz plik konfiguracji. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź sekcję inicjowania obsługi administracyjnej pliku i usuń znaczniki komentarza **ręczne** tryb obsługi administracyjnej. Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia usługi IoT Edge.

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

Zapisz i zamknij plik. 

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o udostępnianiu w pliku konfiguracji, należy ponownie uruchomić demona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opcja 2: Automatyczna aprowizacja

Aby automatycznie aprowizować urządzenia [Konfigurowanie usługi Device Provisioning i pobrania swój identyfikator rejestracji urządzenia] [ lnk-dps] (DPS). Automatyczna aprowizacja działa tylko z urządzeń, które mają mikroukładu Trusted Platform Module (TPM). Na przykład urządzenia Raspberry Pi nie pochodzą z modułem TPM domyślnie. 

Otwórz plik konfiguracji. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź sekcję inicjowania obsługi administracyjnej pliku i usuń znaczniki komentarza **dps** tryb obsługi administracyjnej. Zaktualizuj wartości **scope_id** i **registration_id** wartościami z usługi IoT Hub Device Provisioning Service i urządzenia usługi IoT Edge przy użyciu modułu TPM. 

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

Zapisz i zamknij plik. 

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o udostępnianiu w pliku konfiguracji, należy ponownie uruchomić demona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. Jeśli użyto **automatycznej konfiguracji** kroki należy wykonać kilka dodatkowych kroków, środowisko uruchomieniowe można zarejestrować urządzenia za pomocą usługi IoT hub w Twoim imieniu. Dalsze czynności, zobacz [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na maszynie wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Możesz sprawdzić stan, IoT Edge demona przy użyciu:

```bash
systemctl status iotedge
```

Sprawdź dzienniki demona przy użyciu:

```bash
journalctl -u iotedge --no-pager --no-full
```

I uruchomione moduły z listy:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Porady i sugestie

Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po zainstalowaniu środowiska uruchomieniowego, wyloguj się z komputera, a następnie zaloguj się ponownie na automatyczne aktualizowanie uprawnień. W międzyczasie użyj **"sudo"** przed dowolnego `iotedge` polecenia.

Na urządzeniach z zasobu ograniczone, zdecydowanie zaleca się ustawienie *OptimizeForPerformance* zmiennej środowiskowej, aby *false* zgodnie z instrukcjami w [przewodnik rozwiązywania problemów ][lnk-trouble].

## <a name="next-steps"></a>Kolejne kroki

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z] [ lnk-trouble] strony.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
