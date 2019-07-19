---
title: Instalowanie usługi Azure IoT Edge w systemie Linux | Dokumentacja firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge na urządzeniach z systemem Linux AMD64 systemem Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 822efe2534d49c0995a672232107cc322e547989
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227505"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64)

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze.

Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniu Ubuntu Linux x64 (Intel/AMD) IoT Edge. Listę obsługiwanych systemów operacyjnych AMD64 można znaleźć w [Azure IoT Edge obsługiwanych systemach](support.md#operating-systems) .

> [!NOTE]
> Są pakiety w repozytoriach oprogramowania systemu Linux, z zastrzeżeniem postanowień licencyjnych, znajduje się w każdym pakiecie (/ usr/udostępnianie/docs/*nazwy pakietu*). Przeczytaj postanowienia licencyjne przed przy użyciu pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

## <a name="install-the-latest-version"></a>Zainstaluj najnowszą wersję

Skorzystaj z poniższych sekcji, aby zainstalować najnowszą wersję usługi Azure IoT Edge na urządzeniach. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Zarejestruj kanał repozytorium klucza i oprogramowania firmy Microsoft

Przygotuj urządzenie do instalacji środowiska uruchomieniowego IoT Edge.


Zainstaluj konfigurację repozytorium. Wybierz fragment kodu **16,04** lub **18,04** , który jest odpowiedni dla wydania Ubuntu:

> [!NOTE]
> Upewnij się, że wybierasz fragment kodu z odpowiedniego pola kod dla używanej wersji programu Ubuntu.

* Dla **Ubuntu 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Dla **Ubuntu 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Skopiuj wygenerowaną listę.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Zainstaluj klucz publiczny programu Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

Azure IoT Edge opiera się na zgodnym ze [sterownikiem](https://www.opencontainers.org/) środowiska uruchomieniowego kontenera. W przypadku scenariuszy produkcyjnych zaleca się użycie aparatu opartego [na Moby](https://mobyproject.org/) poniżej. Jest aparat tylko kontenera oficjalnie obsługiwana dzięki usłudze Azure IoT Edge. Obrazy usługi docker CE/EE kontenerów są zgodne ze środowiskiem uruchomieniowym Moby.

Wykonaj aktualizację apt.

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Weryfikacja jądra systemu Linux pod kątem zgodności Moby

Wielu producentów urządzeń osadzonych dostarcza obrazy urządzeń, które zawierają niestandardowe jądra systemu Linux, które mogą nie zawierać funkcji wymaganych przez środowisko uruchomieniowe kontenera. Jeśli wystąpią problemy podczas instalowania zalecanego środowiska uruchomieniowego kontenera [Moby](https://github.com/moby/moby) , może być możliwe Rozwiązywanie problemów z konfiguracją jądra systemu Linux za pomocą skryptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) dostarczonego w oficjalnym [repozytorium usługi Moby](https://github.com/moby/moby) w witrynie GitHub. Uruchamianie następujących poleceń na urządzeniu.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Spowoduje to dostarczenie szczegółowych danych wyjściowych zawierających stan funkcji jądra, które są używane przez środowisko uruchomieniowe Moby. Upewnij się, że wszystkie elementy w obszarze `Generally Necessary` i `Network Drivers` są włączone, aby upewnić się, że jądro jest w pełni zgodne z Moby środowiska uruchomieniowego.  Jeśli zidentyfikowano brakujące funkcje, można je włączyć przez odbudowanie jądra ze źródła i wybranie skojarzonych modułów do uwzględnienia w odpowiednim pliku kernel. config.  Podobnie, jeśli używasz generatora konfiguracji jądra, takiego jak defconfig lub menuconfig, musisz znaleźć i włączyć odpowiednie funkcje i odpowiednio ponownie skompilować jądro.  Po wdrożeniu nowo zmodyfikowanego jądra ponownie uruchom skrypt Check-config, aby sprawdzić, czy zidentyfikowane funkcje zostały pomyślnie włączone.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

**Demon security IoT Edge** zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie pozostałych środowiska uruchomieniowego usługi IoT Edge.

Polecenie instalacji instaluje również wersję standardową **libiothsm** , jeśli jeszcze nie istnieje.

Wykonaj aktualizację apt.

   ```bash
   sudo apt-get update
   ```

Instalowanie demona zabezpieczeń. Pakiet jest zainstalowany na `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Po pomyślnym zainstalowaniu IoT Edge danych wyjściowych zostanie wyświetlony monit o zaktualizowanie pliku konfiguracji. Wykonaj kroki opisane w sekcji [Konfigurowanie demona zabezpieczeń Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) , aby zakończyć Inicjowanie obsługi urządzenia. 

## <a name="install-a-specific-version"></a>Zainstaluj określoną wersję

Jeśli chcesz zainstalować określoną wersję Azure IoT Edge, możesz wskazać pliki składników bezpośrednio z repozytorium usługi IoT Edge GitHub. Wykonaj następujące kroki, aby uzyskać wszystkie składniki IoT Edge na urządzeniu: aparat Moby i interfejs wiersza polecenia, libiothsm i na końcu demona Security IoT Edge.

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa. 

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. W danej wersji mogą nie być dostępne aktualizacje aparatu Moby. Jeśli zobaczysz pliki, które zaczynają się od **Moby-Engine** i **Moby-CLI**, użyj następujących poleceń, aby zaktualizować te składniki. Jeśli nie widzisz żadnych plików Moby i nie masz jeszcze zainstalowanej Moby na urządzeniu, Wróć przez starsze zasoby wydania do momentu ich odnalezienia. 

   1. Znajdź plik **Moby-Engine** pasujący do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję aparatu Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Znajdź plik **Moby-CLI** zgodny z architekturą urządzenia IoT Edge. Interfejs wiersza polecenia Moby jest składnikiem opcjonalnym, ale może być przydatny podczas opracowywania. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza. 

   4. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję interfejsu wiersza polecenia Moby: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Każde wydanie powinno mieć nowe pliki dla IoT Edge Security daemon i hsmlib. Użyj następujących poleceń, aby zaktualizować te składniki. 

   1. Znajdź plik **libiothsm-STD** zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza. 

   2. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Znajdź plik **iotedge** , który jest zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza. 

   4. Aby zainstalować tę wersję demona zabezpieczeń IoT Edge, użyj skopiowanego linku w poniższym poleceniu. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Po pomyślnym zainstalowaniu IoT Edge danych wyjściowych zostanie wyświetlony monit o zaktualizowanie pliku konfiguracji. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zakończyć Inicjowanie obsługi urządzenia. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurowanie demona zabezpieczeń Azure IoT Edge

Konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby połączyć urządzenia fizycznego przy użyciu tożsamości urządzenia, która znajduje się w usłudze Azure IoT hub.

Demon można skonfigurować przy użyciu pliku konfiguracji na `/etc/iotedge/config.yaml`. Plik jest zabezpieczony przed zapisem domyślnie, może być konieczne podwyższonym poziomem uprawnień, aby go edytować.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego.

### <a name="option-1-manual-provisioning"></a>Option 1: Ręczne inicjowanie obsługi

Aby ręcznie zainicjować obsługę urządzeń, należy podać go [parametry połączenia urządzenia](how-to-register-device-portal.md) , możesz utworzyć przez zarejestrowanie nowego urządzenia w usłudze IoT hub.

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

Jeśli użyto **ręcznej konfiguracji** kroki opisane w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge należy pomyślnie aprowizacja i uruchomienie na urządzeniu. Jeśli użyto **automatycznej konfiguracji** kroki należy wykonać kilka dodatkowych kroków, środowisko uruchomieniowe można zarejestrować urządzenia za pomocą usługi IoT hub w Twoim imieniu. Aby uzyskać kolejne kroki, zobacz [Tworzenie i udostępnianie symulowanego urządzenia TPM IoT Edge na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Na urządzeniach z zasobu ograniczone, zdecydowanie zaleca się ustawienie *OptimizeForPerformance* zmiennej środowiskowej, aby *false* zgodnie z instrukcjami w [przewodnik rozwiązywania problemów ](troubleshoot.md).

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

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
