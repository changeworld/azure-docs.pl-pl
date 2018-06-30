---
title: Jak zainstalować krawędzi IoT Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Usługa Azure IoT krawędzi instrukcje instalacji w systemie Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 273bb920b1ef2cac425ba9e636d488c8219ad9d2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37126970"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Zainstaluj środowisko uruchomieniowe krawędzi IoT Azure w systemie Linux (x64)

Środowisko uruchomieniowe Azure IoT krawędzi jest wdrażana na wszystkich urządzeniach IoT krawędzi. Składa się z trzech składników. **Krawędzi IoT zabezpieczeń demon** zapewnia i przechowuje standardów zabezpieczeń na urządzeniu krawędzi. Demon rozpoczyna się na każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta IoT krawędzi. **IoT krawędź agent** ułatwia wdrażanie i monitorowanie modułów na urządzeniu krawędzi, w tym Centrum IoT krawędzi. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe krawędzi IoT Azure w sieci x64 (Intel/AMD) w systemie Linux urządzenia.

>[!NOTE]
>Pakiety w repozytoria oprogramowania systemu Linux są z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/doc/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoje instalacji i używania pakietu oznacza akceptację postanowień niniejszych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie należy używać pakietu.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Zarejestruj źródło repozytorium klucza i oprogramowania firmy Microsoft

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
```

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

Zależy od usługi Azure IoT krawędzi [OCI zgodnego] [ lnk-oci] kontener środowiska uruchomieniowego (np. Docker). Jeśli masz już Docker CE/EE zainstalowanej na urządzeniu krawędzi, można użyć jej do projektowania i testowania Azure IoT krawędzi. 

W scenariuszach produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby] [ lnk-moby] aparat poniżej. To aparat kontenera tylko oficjalnie obsługiwana Azure IoT krawędzi. Obrazy usługi docker CE/EE kontenera są w pełni zgodne ze środowiskiem uruchomieniowym Moby.

*Poniższe instrukcje zainstalować aparat moby i interfejsu wiersza polecenia (CLI). Interfejsu wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń krawędzi IoT Azure

Poniższe polecenia spowoduje także zainstalowanie standardową wersję **iothsmlib** Jeśli nie są już zainstalowane.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Skonfiguruj demona zabezpieczeń krawędzi IoT Azure

Demon można skonfigurować przy użyciu pliku konfiguracji w `/etc/iotedge/config.yaml` urządzenie brzegowe można skonfigurować <!--[automatically via Device Provisioning Service][lnk-dps] or--> ręcznie przy użyciu [ciąg połączenia urządzenia][lnk-dcs].

W przypadku ręcznej konfiguracji, wprowadź ciąg połączenia urządzenia w **inicjowania obsługi administracyjnej** sekcji **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Plik jest zabezpieczony przed zapisem domyślnie, konieczne może być `sudo` go edytować. Na przykład `sudo nano /etc/iotedge/config.yaml`*

Po wprowadzeniu informacje o udostępnianiu w konfiguracji, uruchom ponownie demona:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Można sprawdzić stan przy użyciu demon krawędzi IoT:

```cmd/sh
systemctl status iotedge
```

Przeanalizuj dzienniki demona przy użyciu:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

I uruchomione moduły z listy:

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz problemy ze środowiskiem uruchomieniowym krawędzi poprawnie, instalowanie wyewidencjonowania [Rozwiązywanie problemów z] [ lnk-trouble] strony.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
