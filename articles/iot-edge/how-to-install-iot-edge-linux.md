---
title: Instalowanie Azure IoT Edge w systemie Linux | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Linux lub Ubuntu lub raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: aca417ebbc6f9af80058ddece32842f38918ce60
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964759"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemach Linux opartych na Debian

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego IoT Edge można rozpocząć wdrażanie logiki biznesowej z poziomu chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniu z systemem x64, ARM32 lub ARM64. Pakiety instalacyjne są udostępniane dla Ubuntu Server 16,04, Ubuntu Server 18,04 i raspbian rozciąganie. Listę obsługiwanych systemów operacyjnych i architektur systemu Linux można znaleźć w [Azure IoT Edge obsługiwanych systemach](support.md#operating-systems) .

>[!NOTE]
>Obsługa urządzeń z ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Pakiety w repozytoriach oprogramowania systemu Linux podlegają postanowieniom licencyjnym znajdującym się w każdym pakiecie (/usr/share/doc/*package-name*). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj pakietu.

## <a name="install-the-latest-runtime-version"></a>Zainstaluj najnowszą wersję środowiska uruchomieniowego

Skorzystaj z poniższych sekcji, aby zainstalować najnowszą wersję środowiska uruchomieniowego Azure IoT Edge na urządzeniu. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Rejestrowanie kanału informacyjnego repozytorium i oprogramowania firmy Microsoft

Przygotuj urządzenie do instalacji środowiska uruchomieniowego IoT Edge.

Zainstaluj konfigurację repozytorium. Wybierz polecenie **16,04** lub **18,04** , które odpowiada systemowi operacyjnemu urządzenia:

* **Ubuntu Server 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Rozciągnij raspbian**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
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

Azure IoT Edge opiera się na [zgodnym ze sterownikiem](https://www.opencontainers.org/) środowiska uruchomieniowego kontenera. W przypadku scenariuszy produkcyjnych zaleca się użycie aparatu [opartego na Moby](https://mobyproject.org/) poniżej. Jest to jedyny aparat kontenerów oficjalnie obsługiwany przez Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne ze środowiskiem uruchomieniowym Moby.

Wykonaj aktualizację apt.

   ```bash
   sudo apt-get update
   ```

Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Zainstaluj interfejs wiersza polecenia Moby (CLI). Interfejs wiersza polecenia jest przydatny do programowania, ale opcjonalny dla wdrożeń produkcyjnych.

   ```bash
   sudo apt-get install moby-cli
   ```

Jeśli wystąpią błędy podczas instalowania środowiska uruchomieniowego kontenera Moby, wykonaj kroki opisane w dalszej części tego artykułu, aby [zweryfikować jądro systemu Linux pod kątem zgodności Moby](#verify-your-linux-kernel-for-moby-compatibility). 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń Azure IoT Edge

**Demon security IoT Edge** zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany przy każdym rozruchu i uruchamiania urządzenia przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

Polecenie instalacji instaluje również wersję standardową **libiothsm** , jeśli jeszcze nie istnieje.

Wykonaj aktualizację apt.

   ```bash
   sudo apt-get update
   ```

Zainstaluj demona zabezpieczeń. Pakiet jest instalowany w `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Po pomyślnym zainstalowaniu IoT Edge danych wyjściowych zostanie wyświetlony monit o zaktualizowanie pliku konfiguracji. Wykonaj kroki opisane w sekcji [Konfigurowanie demona zabezpieczeń Azure IoT Edge](#configure-the-security-daemon) , aby zakończyć Inicjowanie obsługi urządzenia. 

## <a name="install-a-specific-runtime-version"></a>Zainstaluj określoną wersję środowiska uruchomieniowego

Jeśli chcesz zainstalować określoną wersję środowiska uruchomieniowego Azure IoT Edge, możesz kierować pliki składników bezpośrednio z repozytorium usługi IoT Edge GitHub. Wykonaj następujące kroki, aby uzyskać wszystkie składniki IoT Edge na urządzeniu: aparat Moby i interfejs wiersza polecenia, libiothsm i na końcu demona Security IoT Edge.

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa. 

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. W danej wersji mogą nie być dostępne aktualizacje aparatu Moby. Jeśli zobaczysz pliki, które zaczynają się od **Moby-Engine** i **Moby-CLI**, użyj następujących poleceń, aby zaktualizować te składniki. Jeśli nie widzisz żadnych plików Moby, Wróć przez starsze zasoby wydania, dopóki nie znajdziesz najnowszej wersji. 

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

## <a name="configure-the-security-daemon"></a>Konfigurowanie demona zabezpieczeń

Skonfiguruj środowisko uruchomieniowe IoT Edge, aby połączyć urządzenie fizyczne z tożsamością urządzenia, która istnieje w usłudze Azure IoT Hub.

Demon można skonfigurować przy użyciu pliku konfiguracyjnego w `/etc/iotedge/config.yaml`. Domyślnie plik jest chroniony przed zapisem, może być konieczne przepodwyższony poziom uprawnień, aby go edytować.

Pojedyncze urządzenie IoT Edge może być inicjowane ręcznie przy użyciu parametrów połączenia urządzenia dostarczonych przez IoT Hub. Można też użyć usługi Device Provisioning, aby automatycznie zainicjować obsługę administracyjną urządzeń, co jest przydatne, gdy masz wiele urządzeń do aprowizacji. W zależności od wybranej opcji aprowizacji wybierz odpowiedni skrypt instalacji.

### <a name="option-1-manual-provisioning"></a>Opcja 1: Ręczne inicjowanie obsługi

Aby ręcznie zainicjować obsługę administracyjną urządzenia, należy podać je za pomocą [parametrów połączenia urządzenia](how-to-register-device.md#register-in-the-azure-portal) , które można utworzyć, rejestrując nowe urządzenie w centrum IoT.

Otwórz plik konfiguracji.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i Usuń komentarz z sekcji **Ręczna konfiguracja inicjowania obsługi** . Zaktualizuj wartość **device_connection_string** za pomocą parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze.

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
Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert`.

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opcja 2: automatyczne Inicjowanie obsługi

Aby automatycznie zainicjować obsługę administracyjną urządzenia, [Skonfiguruj usługę Device Provisioning i Pobierz identyfikator rejestracji urządzenia](how-to-auto-provision-simulated-device-linux.md). Istnieje wiele mechanizmów zaświadczania obsługiwanych przez IoT Edge w przypadku korzystania z automatycznej obsługi administracyjnej, ale wymagania sprzętowe również mają wpływ na wybór. Na przykład urządzenia Raspberry Pi nie są domyślnie dołączone do układu moduł TPM (TPM).

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

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert`.

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

W przypadku użycia czynności **konfiguracyjnych ręcznych** w poprzedniej sekcji środowisko uruchomieniowe IoT Edge powinno zostać pomyślnie zainicjowane i uruchomione na urządzeniu. W przypadku korzystania z kroków **automatycznej konfiguracji** należy wykonać kilka dodatkowych kroków, aby środowisko uruchomieniowe mógł zarejestrować urządzenie w Twoim imieniu w usłudze IoT Hub. Aby uzyskać kolejne kroki, zobacz [Tworzenie i udostępnianie symulowanego urządzenia TPM IoT Edge na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Możesz sprawdzić stan demona IoT Edge:

```bash
systemctl status iotedge
```

Sprawdzanie dzienników demona:

```bash
journalctl -u iotedge --no-pager --no-full
```

Uruchom automatyczne sprawdzanie najbardziej typowych błędów konfiguracji i sieci: 

```bash
sudo iotedge check
```

I listę uruchomionych modułów:

```bash
sudo iotedge list
```

Po zainstalowaniu IoT Edge na urządzeniu jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**. Po utworzeniu pierwszego wdrożenia na urządzeniu zostanie również uruchomiony inny moduł systemu **$edgeHub** . Aby uzyskać więcej informacji, zobacz [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Porady i rozwiązywanie problemów

Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po zainstalowaniu środowiska uruchomieniowego Wyloguj się z komputera i zaloguj się ponownie, aby automatycznie zaktualizować swoje uprawnienia. Do tego czasu należy używać **sudo** przed wszelkimi `iotedge` poleceniami.

W przypadku urządzeń z ograniczonym zasobem zdecydowanie zaleca się ustawienie zmiennej środowiskowej *OptimizeForPerformance* na *wartość false* zgodnie z instrukcjami w [przewodniku rozwiązywania problemów](troubleshoot.md).

Jeśli sieć ma serwer proxy, wykonaj kroki opisane w temacie [Konfigurowanie urządzenia IoT Edge do komunikacji za pomocą serwera proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Weryfikacja jądra systemu Linux pod kątem zgodności Moby

Wielu producentów urządzeń osadzonych dostarcza obrazy urządzeń, które zawierają niestandardowe jądra systemu Linux bez funkcji wymaganych do zapewnienia zgodności środowiska uruchomieniowego kontenera. Jeśli wystąpią problemy podczas instalowania zalecanego środowiska uruchomieniowego kontenera Moby, może być możliwe Rozwiązywanie problemów z konfiguracją jądra systemu Linux za pomocą skryptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficjalnego [repozytorium usługi Moby](https://github.com/moby/moby)w witrynie GitHub. Aby sprawdzić konfigurację jądra, uruchom następujące polecenia na urządzeniu:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Spowoduje to dostarczenie szczegółowych danych wyjściowych zawierających stan funkcji jądra, które są używane przez środowisko uruchomieniowe Moby. Upewnij się, że wszystkie elementy w obszarze `Generally Necessary` i `Network Drivers` są włączone, aby upewnić się, że jądro jest w pełni zgodne z Moby środowiska uruchomieniowego.  Jeśli zidentyfikowano jakiekolwiek brakujące funkcje, należy je włączyć przez ponowne skompilowanie jądra ze źródła i wybranie skojarzonych modułów do uwzględnienia w odpowiednim pliku kernel. config.  Podobnie, jeśli używasz generatora konfiguracji jądra, takiego jak defconfig lub menuconfig, Znajdź i Włącz odpowiednie funkcje i odpowiednio Skompiluj jądro.  Po wdrożeniu nowo zmodyfikowanego jądra ponownie uruchom skrypt Check-config, aby sprawdzić, czy wszystkie wymagane funkcje zostały pomyślnie włączone.


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

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
