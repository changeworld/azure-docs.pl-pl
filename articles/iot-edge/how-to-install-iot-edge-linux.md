---
title: Instalowanie usługi Azure IoT Edge w systemie Linux | Dokumenty firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge na urządzeniach z systemem Linux z systemem Ubuntu lub Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535905"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemach Linux opartych na rozwiązaniu Debian)

Środowisko uruchomieniowe usługi Azure IoT Edge przekształca urządzenie w urządzenie usługi IoT Edge. Środowisko wykonawcze można wdrożyć na urządzeniach tak małych jak Raspberry Pi lub tak dużych jak serwer przemysłowy. Gdy urządzenie jest skonfigurowane ze środowiska wykonawczego usługi IoT Edge, można rozpocząć wdrażanie logiki biznesowej do niego z chmury. Aby dowiedzieć się więcej, zobacz [Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury.](iot-edge-runtime.md)

W tym artykule wymieniono kroki instalowania środowiska wykonawczego usługi Azure IoT Edge na urządzeniu z systemem Linux x64, ARM32 lub ARM64. Dostarczamy pakiety instalacyjne dla Ubuntu Server 16.04, Ubuntu Server 18.04 i Raspbian Stretch. Lista obsługiwanych systemów operacyjnych i architektur w [usłudze Azure IoT Edge](support.md#operating-systems) można znaleźć w systemach obsługiwanych przez usługę Azure IoT Edge.

> [!NOTE]
> Pakiety w repozytoriach oprogramowania Linux podlegają postanowiom licencyjnym znajdującym się w*package-name*każdym pakiecie (/usr/share/doc/package-name). Przed użyciem pakietu należy zapoznać się z postanowieniami licencyjne. Instalacja i korzystanie z pakietu oznacza akceptację niniejszych warunków. Jeśli nie zgadzasz się z postanowieniami licencyjnymi, nie korzystaj z pakietu.

## <a name="install-the-latest-runtime-version"></a>Instalowanie najnowszej wersji środowiska wykonawczego

Użyj poniższych sekcji, aby zainstalować najnowszą wersję środowiska wykonawczego usługi Azure IoT Edge na urządzeniu.

>[!NOTE]
>Obsługa urządzeń ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Rejestrowanie kanału informacyjnego microsoft key and software repozytorium

Przygotuj urządzenie do instalacji środowiska wykonawczego IoT Edge.

Zainstaluj konfigurację repozytorium. Wybierz polecenie **16.04** lub **18.04,** które pasuje do systemu operacyjnego urządzenia:

* **Serwer Ubuntu 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Serwer Ubuntu 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Skopiuj wygenerowaną listę.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instalowanie klucza publicznego microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalowanie środowiska wykonawczego kontenera

Usługa Azure IoT Edge opiera się na środowisko uruchomieniowe kontenera [zgodnego z OCI.](https://www.opencontainers.org/) W scenariuszach produkcyjnych zaleca się użycie aparatu [opartego na moby](https://mobyproject.org/) poniżej. Aparat Moby jest jedynym aparatem kontenerów oficjalnie obsługiwanym przez usługę Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne ze środowiska uruchomieniowego Moby.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zainstaluj silnik Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Zainstaluj interfejs wiersza polecenia Moby (CLI). Cli jest przydatne dla rozwoju, ale opcjonalne dla wdrożeń produkcyjnych.

   ```bash
   sudo apt-get install moby-cli
   ```

Jeśli podczas instalowania środowiska wykonawczego kontenera Moby pojawią się błędy, wykonaj kroki, aby [zweryfikować jądro Linuksa pod kątem zgodności z moby,](#verify-your-linux-kernel-for-moby-compatibility)podano w dalszej części tego artykułu.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

Demon **zabezpieczeń usługi IoT Edge** zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu Usługi IoT Edge. Demon uruchamia się na każdym rozruchu i bootstraps urządzenia, uruchamiając resztę środowiska uruchomieniowego IoT Edge.

Polecenie instalacji instaluje również standardową wersję **libiothsm,** jeśli nie jest jeszcze obecny.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zainstaluj demona zabezpieczeń. Pakiet jest zainstalowany `/etc/iotedge/`w pliku .

   ```bash
   sudo apt-get install iotedge
   ```

Po pomyślnym zainstalowaniu usługi IoT Edge dane wyjściowe zostaną wyświetlone z monitem o zaktualizowanie pliku konfiguracyjnego. Wykonaj kroki opisane w sekcji [Konfigurowanie demona zabezpieczeń,](#configure-the-security-daemon) aby zakończyć inicjowanie obsługi administracyjnej urządzenia.

## <a name="install-a-specific-runtime-version"></a>Instalowanie określonej wersji środowiska wykonawczego

Jeśli chcesz zainstalować określoną wersję programu Moby i środowiska wykonawczego usługi Azure IoT Edge zamiast korzystać z najnowszych wersji, możesz kierować pliki składników bezpośrednio z repozytorium GitHub usługi IoT Edge. Aby uzyskać wszystkie składniki aplikacji IoT Edge na urządzeniu: aparat Moby i interfejsu wiersza polecenia, libiothsm i wreszcie demon zabezpieczeń IoT Edge. Przejdź do następnej [sekcji, Skonfiguruj demona zabezpieczeń](#configure-the-security-daemon), jeśli nie chcesz zmieniać na określoną wersję środowiska uruchomieniowego.

1. Przejdź do [wersji usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i znajdź wersję, którą chcesz kierować.

2. Rozwiń sekcję **Zasoby** dla tej wersji.

3. W danej wersji mogą być aktualizacje silnika Moby. Jeśli widzisz pliki, które zaczynają się od **moby-engine** i **moby-cli,** użyj następujących poleceń, aby zaktualizować te składniki. Jeśli nie widzisz żadnych plików Moby, wróć do starszych zasobów wydania, aż znajdziesz najnowszą wersję.

   1. Znajdź plik **aparatu moby,** który pasuje do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy łącze do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego łącza w następującym poleceniu, aby zainstalować tę wersję aparatu Moby:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Znajdź plik **moby-cli,** który pasuje do architektury urządzenia IoT Edge. Interfejsu wiersza polecenia Moby jest opcjonalnym składnikiem, ale może być pomocne podczas tworzenia. Kliknij prawym przyciskiem myszy łącze do pliku i skopiuj adres łącza.

   4. Użyj skopiowanego łącza w następującym poleceniu, aby zainstalować tę wersję interfejsu wiersza polecenia Moby:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Każda wersja powinna mieć nowe pliki dla demona zabezpieczeń IoT Edge i hsmlib. Użyj następujących poleceń, aby zaktualizować te składniki.

   1. Znajdź plik **libiothsm-std,** który pasuje do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy łącze do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego łącza w następującym poleceniu, aby zainstalować tę wersję hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Znajdź plik **iotedge,** który pasuje do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy łącze do pliku i skopiuj adres łącza.

   4. Użyj skopiowanego łącza w poniższym poleceniu, aby zainstalować tę wersję demona zabezpieczeń usługi IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Po pomyślnym zainstalowaniu usługi IoT Edge dane wyjściowe zostaną wyświetlone z monitem o zaktualizowanie pliku konfiguracyjnego. Wykonaj kroki opisane w następnej sekcji, aby ukończyć inicjowanie obsługi administracyjnej urządzeń.

## <a name="configure-the-security-daemon"></a>Konfigurowanie demona zabezpieczeń

Skonfiguruj środowisko uruchomieniowe usługi IoT Edge, aby połączyć urządzenie fizyczne z tożsamością urządzenia, która istnieje w centrum Azure IoT Hub.

Demona można skonfigurować za pomocą pliku `/etc/iotedge/config.yaml`konfiguracyjnego w pliku . Plik jest domyślnie chroniony przed zapisem, do jego edycji mogą być wymagane podwyższone uprawnienia.

Pojedyncze urządzenie usługi IoT Edge można aprowizować ręcznie przy użyciu ciągu połączeń urządzenia dostarczonego przez centrum IoT Hub. Możesz też użyć usługi inicjowania obsługi administracyjnej urządzeń, aby automatycznie aprowizować urządzenia, co jest przydatne, gdy masz wiele urządzeń do udostępnienia. W zależności od wyboru inicjowania obsługi administracyjnej wybierz odpowiedni skrypt instalacyjny.

### <a name="option-1-manual-provisioning"></a>Opcja 1: Ręczne inicjowanie obsługi administracyjnej

Aby ręcznie aprowizować urządzenie, należy podać mu [ciąg połączenia urządzenia,](how-to-register-device.md#register-in-the-azure-portal) który można utworzyć, rejestrując nowe urządzenie w centrum IoT hub.

Otwórz plik konfiguracyjny.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje inicjowania obsługi administracyjnej pliku i odkomentuj sekcję **Konfiguracji ręcznej inicjowania obsługi administracyjnej.** Zaktualizuj wartość **device_connection_string** za pomocą ciągu połączenia z urządzenia Usługi IoT Edge. Upewnij się, że wszystkie inne sekcje inicjowania obsługi administracyjnej są komentowane. Upewnij się, że **inicjowanie obsługi administracyjnej:** wiersz nie ma poprzedniego odstępu i że elementy zagnieżdżone są wcięte przez dwie spacje.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Aby wkleić zawartość `Shift+Right Click` schowka do nano lub nacisnąć `Shift+Insert`.

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji aprowizacji w pliku konfiguracyjnym uruchom ponownie demona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opcja 2: Automatyczne inicjowanie obsługi administracyjnej

Urządzenia usługi IoT Edge mogą być automatycznie aprowidywane przy użyciu [usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS).](../iot-dps/index.yml) Obecnie usługi IoT Edge obsługuje dwa mechanizmy zaświadczania podczas korzystania z automatycznego inicjowania obsługi administracyjnej, ale wymagania sprzętowe mogą mieć wpływ na wybory. Na przykład urządzenia Raspberry Pi domyślnie nie są wyposażone w układ TPM (Trusted Platform Module). Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Tworzenie i aprowizyjaj obsługi urządzenia usługi IoT Edge za pomocą wirtualnego modułu TPM na maszynie Wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md)
* [Tworzenie i aprowizyja na urządzeniu Usługi IoT Edge przy użyciu certyfikatów X.509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i inicjowania obsługi administracyjnej urządzenia usługi IoT Edge przy użyciu zaświadczania klucza symetrycznego](how-to-auto-provision-symmetric-keys.md)

Te artykuły umożliwiają konfigurowanie rejestracji w dps i generowanie odpowiednich certyfikatów lub kluczy do zaświadczania. Niezależnie od tego, który mechanizm zaświadczania wybierzesz, informacje o inicjowaniu obsługi administracyjnej są dodawane do pliku konfiguracyjnego usługi IoT Edge na urządzeniu usługi IoT Edge.

Otwórz plik konfiguracyjny.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje inicjowania obsługi administracyjnej pliku i odkomentuj sekcję odpowiednią dla mechanizmu zaświadczania. Upewnij się, że wszystkie inne sekcje inicjowania obsługi administracyjnej są komentowane. **Inicjowanie obsługi administracyjnej:** wiersz nie powinien mieć poprzedniego odstępu, a elementy zagnieżdżone powinny być wcięte przez dwie spacje. Zaktualizuj wartość **scope_id** z wartością z wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub i podaj odpowiednie wartości dla pól zaświadczania.

Zaświadczenie tpm:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Zaświadczenie X.509:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Zaświadczenie klucza symetrycznego:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Aby wkleić zawartość `Shift+Right Click` schowka do nano lub nacisnąć `Shift+Insert`.

Zapisz i zamknij plik. `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji aprowizacji w pliku konfiguracyjnym uruchom ponownie demona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Jeśli użyto **ręcznych** kroków konfiguracji w poprzedniej sekcji, środowisko uruchomieniowe usługi IoT Edge powinny zostać pomyślnie zainicjowane i uruchomione na urządzeniu. Jeśli użyto kroki **konfiguracji automatycznej,** należy wykonać kilka dodatkowych kroków, aby środowisko wykonawcze można zarejestrować urządzenie z centrum IoT hub w Twoim imieniu. Aby zapoznać się z kolejnymi krokami, zobacz [Tworzenie i aprowizowywały symulowane urządzenie TPM IoT Edge na maszynie wirtualnej systemu Linux.](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)

Możesz sprawdzić stan demona IoT Edge:

```bash
systemctl status iotedge
```

Sprawdź dzienniki demonów:

```bash
journalctl -u iotedge --no-pager --no-full
```

Uruchom automatyczne sprawdzanie najczęściej występujących błędów konfiguracji i sieci:

```bash
sudo iotedge check
```

Dopóki nie wdrożysz pierwszego modułu w układzie IoT Edge na urządzeniu, moduł systemu **$edgeHub** nie zostanie wdrożony na urządzeniu. W rezultacie automatyczne sprawdzanie zwróci błąd dla `Edge Hub can bind to ports on host` kontroli łączności. Ten błąd można zignorować, chyba że występuje po wdrożeniu modułu na urządzeniu.

Na koniec lista uruchomionych modułów:

```bash
sudo iotedge list
```

Po zainstalowaniu IoT Edge na urządzeniu, jedynym modułem, który powinien być widoczny uruchomiony jest **edgeAgent**. Po utworzeniu pierwszego wdrożenia, drugi moduł systemowy **$edgeHub** zostanie uruchomiony na urządzeniu, jak również. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów usługi IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Porady i rozwiązywanie problemów

Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po zainstalowaniu środowiska wykonawczego wyloguj się z komputera i zaloguj się ponownie, aby automatycznie zaktualizować swoje uprawnienia. Do tego czasu użyj **sudo** przed dowolnymi `iotedge` poleceniami.

Na urządzeniach z ograniczeniami zasobów zdecydowanie zaleca się ustawienie zmiennej środowiskowej *OptimizeForPerformance* na *false* zgodnie z instrukcjami zawartymi w [przewodniku rozwiązywania problemów](troubleshoot.md).

Jeśli sieć z serwerem proxy, wykonaj czynności opisane w [temacie Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Sprawdź, czy jądro Linuksa nie jest kompatybilne z Moby

Wielu producentów urządzeń osadzonych wysyła obrazy urządzeń, które zawierają niestandardowe jądra Linuksa bez funkcji wymaganych do zgodności środowiska wykonawczego kontenera. Jeśli napotkasz problemy podczas instalowania zalecanego środowiska wykonawczego kontenera Moby, możesz rozwiązać problem konfiguracji jądra Linuksa za pomocą skryptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficjalnego [repozytorium Moby GitHub](https://github.com/moby/moby). Uruchom następujące polecenia na urządzeniu, aby sprawdzić konfigurację jądra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

To polecenie zawiera szczegółowe dane wyjściowe, które zawiera stan funkcji jądra, które są używane przez środowisko uruchomieniowe Moby. Należy upewnić się, że `Generally Necessary` `Network Drivers` wszystkie elementy w obszarze i są włączone, aby upewnić się, że jądro jest w pełni zgodne ze środowiska wykonawczego Moby.  Jeśli zidentyfikowano brakujące funkcje, włącz je, odbudowując jądro ze źródła i wybierając skojarzone moduły do włączenia do odpowiedniego jądra .config.  Podobnie, jeśli używasz generatora konfiguracji `defconfig` jądra, takiego jak lub `menuconfig`, znajdź i włącz odpowiednie funkcje i odpowiednio odbuduj jądro.  Po wdrożeniu nowo zmodyfikowanego jądra uruchom ponownie skrypt check-config, aby sprawdzić, czy wszystkie wymagane funkcje zostały pomyślnie włączone.

## <a name="uninstall-iot-edge"></a>Odinstalowywanie przeglądarce IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Linux, użyj następujących poleceń z wiersza polecenia.

Usuń środowisko uruchomieniowe usługi IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po usunięciu środowiska uruchomieniowego IoT Edge kontenery, które utworzono są zatrzymane, ale nadal istnieją na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które z nich pozostają.

```bash
sudo docker ps -a
```

Usuń kontenery z urządzenia, w tym dwa kontenery środowiska wykonawczego.

```bash
sudo docker rm -f <container name>
```

Na koniec usuń środowisko wykonawcze kontenera z urządzenia.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie IoT Edge aprowizowane z zainstalowanym czasem wykonywania, można [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z prawidłową instalacją środowiska wykonawczego IoT Edge, zapoznaj się ze stroną [rozwiązywania problemów.](troubleshoot.md)

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska wykonawczego](how-to-update-iot-edge.md).
