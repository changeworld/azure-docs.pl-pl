---
title: Jak zainstalować krawędzi IoT Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Azure IoT krawędzi instrukcje instalacji w systemie Linux na ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062602"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Zainstaluj środowisko uruchomieniowe krawędzi IoT Azure w systemie Linux (ARM32v7/armhf)

Środowisko uruchomieniowe Azure IoT krawędzi jest wdrażana na wszystkich urządzeniach IoT krawędzi. Składa się z trzech składników. **Krawędzi IoT zabezpieczeń demon** zapewnia i przechowuje standardów zabezpieczeń na urządzeniu krawędzi. Demon rozpoczyna się na każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta IoT krawędzi. **IoT krawędź agent** ułatwia wdrażanie i monitorowanie modułów na urządzeniu krawędzi, w tym Centrum IoT krawędzi. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule przedstawiono kroki, aby zainstalować środowisko uruchomieniowe Azure IoT Edge na urządzeniu krawędzi Linux ARM32v7/armhf (na przykład malina Pi).

>[!NOTE]
>Pakiety w repozytoria oprogramowania systemu Linux są z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/doc/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoje instalacji i używania pakietu oznacza akceptację postanowień niniejszych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie należy używać pakietu.

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

Zależy od usługi Azure IoT krawędzi [OCI zgodnego] [ lnk-oci] kontener środowiska uruchomieniowego (np. Docker). Jeśli masz już Docker CE/EE zainstalowanej na urządzeniu krawędzi, można użyć jej do projektowania i testowania Azure IoT krawędzi. 

W scenariuszach produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby] [ lnk-moby] aparat poniżej. To aparat kontenera tylko oficjalnie obsługiwana Azure IoT krawędzi. Obrazy usługi docker CE/EE kontenera są w pełni zgodne ze środowiskiem uruchomieniowym Moby.

Poniższe polecenia zainstalować aparat moby i interfejsu wiersza polecenia (CLI). Interfejsu wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.

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

## <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń krawędzi IoT

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
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz problemy ze środowiskiem uruchomieniowym krawędzi poprawnie, instalowanie wyewidencjonowania [Rozwiązywanie problemów z] [ lnk-trouble] strony.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md