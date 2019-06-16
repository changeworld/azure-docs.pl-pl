---
title: Instalowanie usługi Azure IoT Edge w systemie Linux ARM32 | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje dotyczące instalacji w systemie Linux na urządzeniach ARM32, takich jak Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kgremban
ms.openlocfilehash: 6c22680102c57fdfc3d25beb19e5bc9847995b28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65152730"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu z systemem Linux ARM32v7/armhf IoT Edge. Na przykład następujące kroki, będzie działać w przypadku urządzeń Raspberry Pi. Aby uzyskać listę obsługiwanych systemów operacyjnych ARM32 zobacz [usługi Azure IoT Edge obsługiwane systemy](support.md#operating-systems). 

>[!NOTE]
>Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

Usługa Azure IoT Edge opiera się na [zgodnego z OCI](https://www.opencontainers.org/) kontener środowiska uruchomieniowego. Na potrzeby scenariuszy produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby](https://mobyproject.org/) aparatu przedstawione poniżej. Jest aparat tylko kontenera oficjalnie obsługiwana dzięki usłudze Azure IoT Edge. Obrazy kontenera usługi docker CE/EE są zgodne ze środowiskiem uruchomieniowym na podstawie Moby.

Poniższe polecenia instalują zarówno aparat oparty na Moby, jak i interfejs wiersza polecenia (CLI). Interfejs wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.

```bash
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

**Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi IoT Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie pozostałych środowiska uruchomieniowego usługi IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="connect-your-device-to-an-iot-hub"></a>Podłącz urządzenie do Centrum IoT hub 

Konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby połączyć urządzenia fizycznego przy użyciu tożsamości urządzenia, która znajduje się w usłudze Azure IoT hub. 

Demon można skonfigurować przy użyciu pliku konfiguracji na `/etc/iotedge/config.yaml`. Plik jest zabezpieczony przed zapisem domyślnie, dlatego warto podwyższonym poziomem uprawnień, aby go edytować.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

### <a name="option-1-manual-provisioning"></a>Opcja 1: Aprowizacja ręczna

Aby ręcznie zainicjować obsługę urządzeń, należy podać go [parametry połączenia urządzenia](how-to-register-device-portal.md) , możesz utworzyć przez zarejestrowanie nowego urządzenia usługi IoT Edge w usłudze IoT hub.

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

Aby automatycznie aprowizować urządzenia [Konfigurowanie usługi Device Provisioning i pobrania swój identyfikator rejestracji urządzenia](how-to-auto-provision-simulated-device-linux.md). Automatyczna aprowizacja działa tylko z urządzeń, które mają mikroukładu Trusted Platform Module (TPM). Na przykład urządzenia Raspberry Pi nie pochodzą z modułem TPM domyślnie. 

Otwórz plik konfiguracji. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź sekcję inicjowania obsługi administracyjnej pliku i usuń znaczniki komentarza **dps** tryb obsługi administracyjnej. Zaktualizuj wartości **scope_id** i **registration_id** wartościami z usługi IoT Hub Device Provisioning oraz urządzenia usługi IoT Edge przy użyciu modułu TPM. 

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

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. Lub, jeśli użyto **automatycznej konfiguracji** kroki należy wykonać kilka dodatkowych kroków, środowisko uruchomieniowe można zarejestrować urządzenia za pomocą usługi IoT hub w Twoim imieniu. Dalsze czynności, zobacz [tworzenie i aprowizowanie symulowanego urządzenia TPM IoT Edge na maszynie wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Na urządzeniach z zasobu ograniczone, zdecydowanie zaleca się ustawienie *OptimizeForPerformance* zmiennej środowiskowej, aby *false* zgodnie z instrukcjami w [przewodnik rozwiązywania problemów ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Jeśli sieci, na którym działa serwer proxy, wykonaj kroki opisane w [skonfigurować urządzenie usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Odinstalowywanie usługi IoT Edge

Jeśli chcesz usunąć instalację usługi IoT Edge na urządzeniu z systemem Linux, użyj następujących poleceń w wierszu polecenia. 

Usuń środowisko uruchomieniowe usługi IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Po usunięciu środowiska uruchomieniowego usługi IoT Edge kontenera, do którego utworzona zostaną zatrzymane, ale nadal istnieje na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które pozostają. 

```bash
sudo docker ps -a
```

Usuwanie kontenerów na urządzeniu, w tym dwóch kontenerów środowiska wykonawczego. 

```bash
sudo docker rm -f <container name>
```

Na koniec usunąć kontener środowiska uruchomieniowego na urządzeniu. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy ze środowiskiem uruchomieniowym usługi IoT Edge instalacji prawidłowo, należy zapoznać się [Rozwiązywanie problemów z](troubleshoot.md#stability-issues-on-resource-constrained-devices) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).
