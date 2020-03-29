---
title: Aprowizuj urządzenie z wirtualnym modułem TPM na maszynie VM systemu Linux — usługa Azure IoT Edge
description: Użyj symulowanego modułu TPM na maszynie Wirtualnej systemu Linux, aby przetestować usługę inicjowania obsługi administracyjnej urządzeń platformy Azure dla usługi Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511063"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Tworzenie i aprowizyja na urządzeniu usługi IoT Edge za pomocą wirtualnego modułu TPM na maszynie wirtualnej systemu Linux

Urządzenia usługi Azure IoT Edge mogą być automatycznie aprowidzone przy użyciu [usługi inicjowania obsługi administracyjnej urządzeń.](../iot-dps/index.yml) Jeśli nie znasz procesu automatycznego inicjowania obsługi administracyjnej, przejrzyj [pojęcia automatycznego inicjowania obsługi administracyjnej](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem.

W tym artykule pokazano, jak przetestować automatyczne inicjowanie obsługi administracyjnej na symulowanym urządzeniu Z krawędzią IoT w następujących krokach:

* Utwórz maszynę wirtualną systemu Linux (V) w funkcji Hyper-V z symulowanym modułem TPM (Trusted Platform Module) dla zabezpieczeń sprzętu.
* Utwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzeń (DPS) centrum IoT.
* Tworzenie indywidualnej rejestracji dla urządzenia
* Zainstaluj środowisko wykonawcze usługi IoT Edge i podłącz urządzenie do centrum IoT Hub

> [!TIP]
> W tym artykule opisano sposób testowania aprowizacji dps przy użyciu symulatora modułu TPM, ale wiele z nich dotyczy fizycznego sprzętu TPM, takiego jak [Infineon&trade; OPTIGA TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), urządzenie z certyfikatem platformy Azure dla IoT.
>
> Jeśli używasz urządzenia fizycznego, możesz przejść do sekcji [Pobieranie obsługi administracyjnej z urządzenia fizycznego](#retrieve-provisioning-information-from-a-physical-device) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer dewelopera systemu Windows z [włączoną funkcją Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule używa się systemu Windows 10 z uruchomioną maszyną wirtualną Ubuntu Server.
* Aktywne Centrum IoT.
* W przypadku korzystania z symulowanego modułu TPM, [visual studio](https://visualstudio.microsoft.com/vs/) 2015 lub nowsze z włączonym obciążeniem "Programowy pulpitu z włączonym [c++".](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> Moduł TPM 2.0 jest wymagany podczas korzystania z zaświadczania modułu TPM z dps i może być używany tylko do tworzenia rejestracji indywidualnych, a nie grupowych.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Tworzenie maszyny wirtualnej systemu Linux za pomocą wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną systemu Linux w funkcji Hyper-V. Ta maszyna wirtualna została skonfigurowana z symulowanym modułem TPM, dzięki czemu można jej używać do testowania sposobu automatycznego inicjowania obsługi administracyjnej z usługą IoT Edge.

### <a name="create-a-virtual-switch"></a>Tworzenie wirtualnego przełącznika

Przełącznik wirtualny umożliwia maszynie wirtualnej łączenie się z siecią fizyczną.

1. Otwórz Menedżera funkcji Hyper-V na komputerze z systemem Windows.

2. W menu **Akcje** wybierz polecenie **Wirtualny Menedżer przełączników**.

3. Wybierz **zewnętrzny** przełącznik wirtualny, a następnie wybierz pozycję **Utwórz przełącznik wirtualny**.

4. Nadaj nowemu wirtualnemu przełącznikowi nazwę, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia jest ustawiony na **Sieć zewnętrzna,** a następnie **wybierz**ok .

5. W wyskakującym okienku z ostrzeżeniem, że łączność sieciowa może zostać zakłócona. Wybierz **pozycję Tak,** aby kontynuować.

Jeśli podczas tworzenia nowego przełącznika wirtualnego widoczne są błędy, upewnij się, że żadne inne przełączniki nie używają adaptera Ethernet i że żadne inne przełączniki nie używają tej samej nazwy.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia na maszynie wirtualnej i zapisz go lokalnie. Na przykład [serwer Ubuntu](https://www.ubuntu.com/download/server).

2. Ponownie w Menedżerze funkcji Hyper-V wybierz polecenie **Nowa** > **maszyna wirtualna** w menu **Akcje.**

3. Ukończ **Kreatora nowej maszyny wirtualnej** następującymi określonymi konfiguracjami:

   1. **Określ generowanie**: Wybierz **generację 2**. Maszyny wirtualne generacji 2 mają włączoną wirtulizację zagnieżdżoną, która jest wymagana do uruchamiania usługi IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci:** Ustaw wartość **połączenia** na przełącznik wirtualny utworzony w poprzedniej sekcji.
   3. **Opcje instalacji**: Wybierz **pozycję Zainstaluj system operacyjny z pliku obrazu rozruchowego** i przejdź do pliku obrazu dysku zapisanego lokalnie.

4. Wybierz **pozycję Zakończ** w kreatorze, aby utworzyć maszynę wirtualną.

Utworzenie nowej maszyny Wirtualnej może potrwać kilka minut.

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

Po utworzeniu maszyny Wirtualnej otwórz jej ustawienia, aby włączyć moduł TPM (virtual trusted platform module), który umożliwia automatyczne aprowizowanie urządzenia.

1. Wybierz maszynę wirtualną, a następnie otwórz jej **Ustawienia**.

2. Przejdź do obszaru **Security** (Zabezpieczenia).

3. Odznacz pozycję **Włącz bezpieczny rozruch**.

4. Sprawdź **włącz moduł zaufanej platformy**.

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchamianie maszyny wirtualnej i zbieranie danych modułu TPM

Na maszynie wirtualnej skompiluj narzędzie, za pomocą którego można pobrać **identyfikator rejestracji** urządzenia i **klucz poręczenia.**

1. Uruchom maszynę wirtualną i połącz się z nią.

1. Postępuj zgodnie z instrukcjami w maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić komputer.

1. Zaloguj się do maszyny Wirtualnej, a następnie wykonaj kroki opisane w [Konfigurowanie środowiska programistycznego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i utworzenia zestawu SDK urządzenia Usługi Azure IoT dla języka C.

   >[!TIP]
   >W trakcie tego artykułu skopiujesz i wklej z maszyny wirtualnej, co nie jest łatwe za pośrednictwem aplikacji połączenia Menedżera funkcji Hyper-V. Możesz połączyć się z maszyną wirtualną za pośrednictwem Menedżera funkcji `ifconfig`Hyper-V raz, aby pobrać jego adres IP: . Następnie można użyć adresu IP, aby połączyć się za pośrednictwem SSH: `ssh <username>@<ipaddress>`.

1. Uruchom następujące polecenia, aby utworzyć narzędzie zestawu SDK, które pobiera informacje o inicjowaniu obsługi administracyjnej urządzenia z symulatora modułu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. W oknie polecenia przejdź `azure-iot-sdk-c` do katalogu i uruchom symulator modułu TPM. Nasłuchuje on przez gniazdo na portach 2321 i 2322. Nie należy zamykać tego okna polecenia; trzeba będzie zachować ten symulator działa.

   W `azure-iot-sdk-c` katalogu uruchom następujące polecenie, aby uruchomić symulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Za pomocą programu Visual Studio `cmake` otwórz rozwiązanie `azure_iot_sdks.sln`wygenerowane w katalogu o nazwie i skompiluj je za pomocą polecenia **Kompilacja rozwiązania** w menu **Kompilacja.**

1. W okienku **Eksplorator rozwiązań** w programie Visual Studio przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**.

1. Uruchom rozwiązanie przy użyciu jednego z poleceń **Start** w menu **Debugowania.** Okno danych wyjściowych wyświetla **identyfikator rejestracji** symulatora modułu TPM i **klucz poręczenia,** który należy skopiować do użycia później podczas tworzenia indywidualnej rejestracji dla urządzenia w oknie Można zamknąć to okno (z identyfikatorem rejestracji i kluczem poręczenia), ale pozostawić uruchomione okno symulatora modułu TPM.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Pobieranie informacji aprowizacji z urządzenia fizycznego

Na urządzeniu skompiluj narzędzie, za pomocą którego można pobrać informacje o inicjowaniu obsługi administracyjnej urządzenia.

1. Wykonaj kroki opisane w [konfigurowaniu środowiska programistycznego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i utworzenia zestawu SDK urządzenia Usługi Azure IoT dla języka C.

1. Uruchom następujące polecenia, aby utworzyć narzędzie zestawu SDK, które pobiera informacje o inicjowaniu obsługi administracyjnej urządzenia z urządzenia TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Skopiuj wartości dla **identyfikatora rejestracji** i **klucza poręczenia**. Te wartości są używane do tworzenia indywidualnej rejestracji dla urządzenia w dps.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub

Utwórz nowe wystąpienie usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi Usługi IoT na platformie Azure i połącz je z centrum IoT hub. Postępuj zgodnie z instrukcjami w [konfigurowaniu dps centrum IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po uruchomieniu usługi inicjowania obsługi administracyjnej urządzeń skopiuj wartość **zakresu identyfikatora** ze strony przeglądowej. Ta wartość jest używana podczas konfigurowania środowiska wykonawczego usługi IoT Edge.

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji DPS

Pobierz informacje aprowizacji z maszyny wirtualnej i użyj ich do utworzenia indywidualnej rejestracji w usłudze inicjowania obsługi administracyjnej urządzeń.

Podczas tworzenia rejestracji w dps, masz możliwość zadeklarowania **stanu bliźniaczej reprezentacji urządzenia początkowego**. W bliźniaczej reprezentacji urządzenia można ustawić znaczniki, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, takiej jak region, środowisko, lokalizacja lub typ urządzenia. Tagi te są używane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

2. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**.

3. Wybierz **pozycję Dodaj rejestrację indywidualną,** a następnie wykonaj następujące czynności, aby skonfigurować rejestrację:  

   1. W przypadku **opcji Mechanizm**wybierz **TPM**.

   2. Podaj **klucz poręczenia i** **identyfikator rejestracji** skopiowany z maszyny wirtualnej.

      > [!TIP]
      > Jeśli używasz fizycznego urządzenia TPM, musisz określić **klucz poręczenia,** który jest unikatowy dla każdego układu TPM i jest uzyskiwany od producenta układu TPM skojarzonego z nim. Unikatowy **identyfikator rejestracji** dla urządzenia TPM można uzyskać, na przykład tworząc skrót SHA-256 klucza poręczenia.

   3. Wybierz **wartość True,** aby zadeklarować, że ta maszyna wirtualna jest urządzeniem usługi IoT Edge.

   4. Wybierz połączone centrum **IoT Hub,** do którego chcesz podłączyć urządzenie. Można wybrać wiele koncentratorów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   5. Podaj identyfikator urządzenia, jeśli chcesz. Identyfikatory urządzeń można użyć do kierowania poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podasz identyfikatora urządzenia, zostanie użyty identyfikator rejestracji.

   6. Dodaj wartość znacznika do **stanu bliźniaczej reprezentacji urządzenia początkowego,** jeśli chcesz. Tagów można używać do grup docelowych urządzeń do wdrażania modułu. Przykład:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Wybierz **pozycję Zapisz**.

Teraz, gdy istnieje rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie aprowizować urządzenie podczas instalacji.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska wykonawczego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki są uruchamiane w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchomić kod na krawędzi. Zainstaluj środowisko uruchomieniowe usługi IoT Edge na maszynie wirtualnej.

Poznaj zakres **identyfikatora** DPS i **identyfikator rejestracji** urządzenia przed rozpoczęciem artykułu, który pasuje do typu urządzenia. Jeśli zainstalowano przykładowy serwer Ubuntu, użyj instrukcji **x64.** Upewnij się, aby skonfigurować środowisko uruchomieniowe IoT Edge dla automatycznego, a nie ręcznego inicjowania obsługi administracyjnej.

[Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Udzielanie ioT Edge dostępu do modułu TPM

Aby środowisko uruchomieniowe IoT Edge było automatycznie aprowizować urządzenie, musi mieć dostęp do modułu TPM.

Można udzielić tpm dostępu do środowiska uruchomieniowego usługi IoT Edge, zastępując ustawienia systemd, tak aby usługa **iotedge** ma uprawnienia główne. Jeśli nie chcesz podnosić poziomu uprawnień usługi, możesz również użyć następujących kroków, aby ręcznie zapewnić dostęp do modułu TPM.

1. Znajdź ścieżkę pliku do modułu sprzętowego modułu TPM na urządzeniu i zapisz ją jako zmienną lokalną.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Utwórz nową regułę, która zapewni środowisko uruchomieniowe IoT Edge dostęp do tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otwórz plik reguł.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Skopiuj następujące informacje o dostępie do pliku reguł.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Zapisz i zamknij plik.

6. Wyzwolić system udev, aby ocenić nową regułę.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Sprawdź, czy reguła została pomyślnie zastosowana.

   ```bash
   ls -l /dev/tpm0
   ```

   Pomyślne wyjście wygląda następująco:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Jeśli nie widzisz, że zostały zastosowane odpowiednie uprawnienia, spróbuj ponownie uruchomić komputer, aby odświeżyć udev.

## <a name="restart-the-iot-edge-runtime"></a>Ponowne uruchamianie ioT Edge

Uruchom ponownie środowisko uruchomieniowe usługi IoT Edge, aby odebrało wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

Sprawdź, czy środowisko uruchomieniowe IoT Edge jest uruchomione.

   ```bash
   sudo systemctl status iotedge
   ```

Jeśli widzisz błędy inicjowania obsługi administracyjnej, może być, że zmiany konfiguracji nie zostały jeszcze wprowadzone. Spróbuj ponownie uruchomić demona IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Możesz też ponownie uruchomić maszynę wirtualną, aby sprawdzić, czy zmiany zaczną obowiązywać od nowego początku.

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Jeśli środowisko wykonawcze zostało pomyślnie uruchomione, możesz przejść do centrum IoT Hub i zobaczyć, że nowe urządzenie zostało automatycznie aprowizowane. Teraz urządzenie jest gotowe do uruchamiania modułów IoT Edge.

Sprawdź stan demona IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdź dzienniki demonów.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista uruchomionych modułów.

```cmd/sh
iotedge list
```

Można sprawdzić, czy została użyta indywidualna rejestracja utworzona w usłudze inicjowania obsługi administracyjnej urządzeń. Przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń w witrynie Azure portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Należy zauważyć, że stan rejestracji jest **przypisany** i identyfikator urządzenia jest wyświetlany.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi inicjowania obsługi administracyjnej urządzeń umożliwia ustawienie identyfikatora urządzenia i bliźniaczych tagów urządzenia w tym samym czasie, co aprowizowanie nowego urządzenia. Za pomocą tych wartości można kierować reklamy na poszczególne urządzenia lub grupy urządzeń za pomocą automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure.](how-to-deploy-monitor-cli.md)
