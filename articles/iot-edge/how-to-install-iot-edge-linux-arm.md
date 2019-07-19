---
title: Instalowanie Azure IoT Edge w systemie Linux ARM32 | Microsoft Docs
description: Azure IoT Edge instrukcje dotyczące instalacji systemu Linux na urządzeniach ARM32, takich jak Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224708"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux (ARM32v7/armhf)

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniu z systemem Linux ARM32v7/armhf IoT Edge. Na przykład te kroki będą działały w przypadku urządzeń z Raspberry Pi. Listę obsługiwanych systemów operacyjnych ARM32 można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md#operating-systems). 

>[!NOTE]
>Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="install-the-latest-version"></a>Zainstaluj najnowszą wersję

Skorzystaj z poniższych sekcji, aby zainstalować najnowszą wersję usługi Azure IoT Edge na urządzeniach ARM systemu Linux. 

### <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

Azure IoT Edge opiera się na zgodnym ze [sterownikiem](https://www.opencontainers.org/) środowiska uruchomieniowego kontenera. Na potrzeby scenariuszy produkcyjnych zdecydowanie zaleca się używasz [na podstawie Moby](https://mobyproject.org/) aparatu przedstawione poniżej. Jest aparat tylko kontenera oficjalnie obsługiwana dzięki usłudze Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne z środowiskiem uruchomieniowym opartym na Moby.

Następujące polecenia instalują zarówno aparat oparty na Moby, jak i interfejs wiersza polecenia (CLI). Interfejs wiersza polecenia jest przydatne do tworzenia aplikacji, ale opcjonalny w przypadku wdrożeń produkcyjnych.

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

### <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń IoT Edge

**Demon security IoT Edge** zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie pozostałych środowiska uruchomieniowego usługi IoT Edge. 


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

Po pomyślnym zainstalowaniu IoT Edge danych wyjściowych zostanie wyświetlony monit o zaktualizowanie pliku konfiguracji. Wykonaj kroki opisane w sekcji [Konfigurowanie demona zabezpieczeń Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) , aby zakończyć Inicjowanie obsługi urządzenia. 

## <a name="install-a-specific-version"></a>Zainstaluj określoną wersję

Jeśli chcesz zainstalować określoną wersję Azure IoT Edge, możesz wskazać pliki składników bezpośrednio z repozytorium usługi IoT Edge GitHub. Użyj tych samych `curl` poleceń wymienionych w poprzednich sekcjach, aby uzyskać wszystkie składniki IoT Edge na urządzeniu: aparat Moby i interfejs wiersza polecenia, libiothsm i na końcu demona zabezpieczeń IoT Edge. Jedyną różnicą jest zamienienie adresów URL **aka.MS** z linkami bezpośrednio wskazującymi na wersję każdego składnika, który ma być używany.

Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa. Rozwiń sekcję **zasoby** dla wersji i wybierz pliki, które pasują do architektury urządzenia IoT Edge. Każda wersja IoT Edge zawiera pliki **iotedge** i **libiothsm** . Nie wszystkie wersje obejmują **Moby-Engine** lub **Moby-CLI**. Jeśli nie masz jeszcze zainstalowanego aparatu kontenera Moby, zapoznaj się ze starszymi wersjami, dopóki nie znajdziesz go zawierającego składniki Moby. 

Po pomyślnym zainstalowaniu IoT Edge danych wyjściowych zostanie wyświetlony monit o zaktualizowanie pliku konfiguracji. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zakończyć Inicjowanie obsługi urządzenia. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurowanie demona zabezpieczeń Azure IoT Edge

Konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby połączyć urządzenia fizycznego przy użyciu tożsamości urządzenia, która znajduje się w usłudze Azure IoT hub. 

Demon można skonfigurować przy użyciu pliku konfiguracji na `/etc/iotedge/config.yaml`. Plik jest domyślnie chroniony przed zapisem, dlatego możesz potrzebować podniesionych uprawnień, aby go edytować.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

### <a name="option-1-manual-provisioning"></a>Option 1: Ręczne inicjowanie obsługi

Aby ręcznie zainicjować obsługę administracyjną urządzenia, należy podać je za pomocą [parametrów połączenia urządzenia](how-to-register-device-portal.md) , które można utworzyć, rejestrując nowe urządzenie IoT Edge w centrum IoT.

Otwórz plik konfiguracji. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i Usuń komentarz z sekcji **Ręczna konfiguracja inicjowania obsługi** . Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia usługi IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Zapisz i zamknij plik. 

`CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o udostępnianiu w pliku konfiguracji, należy ponownie uruchomić demona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opcja 2: Automatyczne Inicjowanie obsługi

Aby automatycznie aprowizować urządzenia [Konfigurowanie usługi Device Provisioning i pobrania swój identyfikator rejestracji urządzenia](how-to-auto-provision-simulated-device-linux.md). Istnieje wiele mechanizmów zaświadczania obsługiwanych przez IoT Edge w przypadku korzystania z automatycznej obsługi administracyjnej, ale wymagania sprzętowe również mają wpływ na wybór. Na przykład urządzenia Raspberry Pi nie są domyślnie dołączone do układu moduł TPM (TPM).

Otwórz plik konfiguracji. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i usuń znaczniki komentarza odpowiednie dla mechanizmu zaświadczania. W przypadku korzystania z zaświadczania modułu TPM należy na przykład zaktualizować wartości **scope_id** i **registration_id** przy użyciu wartości z usługi IoT Hub Device Provisioning i urządzenia IoT Edge z modułem TPM.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Zapisz i zamknij plik. 

`CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o udostępnianiu w pliku konfiguracji, należy ponownie uruchomić demona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. W przypadku korzystania z automatycznych kroków **konfiguracji** należy wykonać kilka dodatkowych kroków, aby środowisko uruchomieniowe może zarejestrować urządzenie w Twoim imieniu w usłudze IoT Hub. Aby uzyskać kolejne kroki, zobacz [Tworzenie i udostępnianie symulowanego urządzenia TPM IoT Edge na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Linux, użyj następujących poleceń w wierszu polecenia. 

Usuń środowisko uruchomieniowe usługi IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Po usunięciu IoT Edge środowiska uruchomieniowego tworzony kontener zostanie zatrzymany, ale nadal istnieje na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które pozostaną. 

```bash
sudo docker ps -a
```

Usuń kontenery z urządzenia, w tym dwa kontenery środowiska uruchomieniowego. 

```bash
sudo docker rm -f <container name>
```

Na koniec Usuń środowisko uruchomieniowe kontenera z urządzenia. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md#stability-issues-on-resource-constrained-devices) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
