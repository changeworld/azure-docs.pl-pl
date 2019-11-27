---
title: Automatyczne Inicjowanie obsługi urządzeń z systemem Linux przy użyciu platformy DPS — Azure IoT Edge | Microsoft Docs
description: Użyj symulowanego modułu TPM na maszynie Wirtualnej systemu Linux do testowania usługi Azure IoT Edge w usłudze Azure Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457170"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux

Azure IoT Edge urządzeń można automatycznie zainicjować przy użyciu [usługi Device Provisioning](../iot-dps/index.yml). Jeśli nie znasz procesu samoobsługowego inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z [pojęciami autouzupełniania](../iot-dps/concepts-auto-provisioning.md) .

W tym artykule pokazano, jak przetestować funkcję autoaprowizacji na symulowanym urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz maszynę wirtualną systemu Linux (VM) w funkcji Hyper-V za pomocą symulowanego Trusted Platform Module (TPM) dotyczące zabezpieczeń sprzętowych.
* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzyć rejestrację indywidualną dla urządzenia
* Zainstalować środowisko uruchomieniowe usługi IoT Edge i połączyć to urządzenie do Centrum IoT Hub

> [!NOTE]
> Moduł TPM 2,0 jest wymagany w przypadku korzystania z zaświadczania TPM z usługą DPS i może być używany tylko do tworzenia pojedynczych, nie grup, rejestracji.

> [!TIP]
> W tym artykule opisano sposób testowania aprowizacji usługi DPS przy użyciu symulatora modułu TPM, ale większość z nich ma zastosowanie do fizycznego sprzętu modułu TPM, takiego jak [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), urządzenia z certyfikatem platformy Azure dla IoT.
>
> Jeśli używasz urządzenia fizycznego, możesz przejść do sekcji [pobieranie informacji o aprowizacji z urządzenia fizycznego](#retrieve-provisioning-information-from-a-physical-device) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna deweloperskia systemu Windows z [włączoną funkcją Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule używany jest system Windows 10 uruchomionej maszyny Wirtualnej systemu Ubuntu Server.
* Aktywnym Centrum IoT.
* W przypadku korzystania z symulowanego modułu TPM, [programu Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 lub nowszego z włączonym obciążeniem [ C++"Programowanie aplikacji klasycznych"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Utwórz maszynę wirtualną systemu Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną z systemem Linux w ramach funkcji Hyper-V. Ta maszyna wirtualna została skonfigurowana przy użyciu symulowanego modułu TPM, aby można było użyć jej do testowania, w jaki sposób Automatyczna obsługa administracyjna współpracuje z IoT Edge. 

### <a name="create-a-virtual-switch"></a>Utwórz przełącznik wirtualny

Przełącznik wirtualny pozwala połączyć się z siecią fizyczną, do maszyny wirtualnej.

1. Otwórz Menedżera funkcji Hyper-V na komputerze z systemem Windows. 

2. W menu **Akcje** wybierz pozycję **Menedżer przełącznika wirtualnego**. 

3. Wybierz **zewnętrzny** przełącznik wirtualny, a następnie wybierz pozycję **Utwórz przełącznik wirtualny**. 

4. Nadaj nowemu przełącznikowi wirtualnemu nazwę, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia to **Sieć zewnętrzna**, a następnie wybierz przycisk **OK**.

5. Okno podręczne wyświetli ostrzeżenie, może zostać przerwane połączenie sieciowe. Wybierz pozycję **tak** , aby kontynuować. 

Jeśli widzisz błędy podczas tworzenia nowego przełącznika wirtualnego, upewnij się, że żadne inne przełączniki korzystają karta ethernet i że żadne inne przełączniki, jak używać tej samej nazwie. 

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i zapisać je lokalnie. Na przykład [serwer Ubuntu](https://www.ubuntu.com/download/server). 

2. W Menedżerze funkcji Hyper-V ponownie wybierz pozycję **nowa** > **maszyna wirtualna** w menu **Akcje** .

3. Ukończ pracę **Kreatora nowej maszyny wirtualnej** z następującymi określonymi konfiguracjami:

   1. **Określ generację**: Wybierz **generację 2**. Maszyny wirtualne generacji 2 mają włączoną funkcję wirtualizacji zagnieżdżonej, która jest wymagana do uruchamiania IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci**: Ustaw wartość ustawienia **połączenie** z przełącznikiem wirtualnym, który został utworzony w poprzedniej sekcji. 
   3. **Opcje instalacji**: wybierz opcję **Zainstaluj system operacyjny z pliku obrazu rozruchowego** i przejdź do pliku obrazu dysku zapisanego lokalnie.

4. Wybierz pozycję **Zakończ** w kreatorze, aby utworzyć maszynę wirtualną.

Może upłynąć kilka minut na utworzenie nowej maszyny Wirtualnej. 

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

Po utworzeniu maszyny wirtualnej Otwórz jej ustawienia, aby włączyć moduł TPM (Trusted Platform Module), który umożliwia samoobsługowe Inicjowanie obsługi urządzenia.

1. Wybierz maszynę wirtualną, a następnie otwórz jej **Ustawienia**.

2. Przejdź do obszaru **Security** (Zabezpieczenia). 

3. Usuń zaznaczenie pola wyboru **Włącz bezpieczny rozruch**.

4. Zaznacz **opcję włącz moduł TPM**. 

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchom maszynę wirtualną i zbieranie danych modułu TPM

Na maszynie wirtualnej Utwórz narzędzie, za pomocą którego można pobrać **Identyfikator rejestracji** i **Klucz poręczenia**urządzenia.

1. Uruchom maszynę wirtualną i nawiąż z nią połączenie.

1. Postępuj zgodnie z monitami na maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić maszynę.

1. Zaloguj się do maszyny wirtualnej, a następnie wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i SKOMPILOWANIA zestawu SDK urządzeń Azure IoT dla języka C.

   >[!TIP]
   >W tym artykule opisano kopiowanie do i wklejanie z maszyny wirtualnej, co nie jest łatwe w aplikacji połączenia Menedżera funkcji Hyper-V. Aby pobrać swój adres IP, możesz połączyć się z maszyną wirtualną za pomocą Menedżera funkcji Hyper-V: `ifconfig`. Następnie można użyć adresu IP, aby nawiązać połączenie za pośrednictwem protokołu SSH: `ssh <username>@<ipaddress>`.

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizacji urządzenia z symulatora modułu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. W oknie wiersza polecenia przejdź do katalogu `azure-iot-sdk-c` i uruchom symulator modułu TPM. Nasłuchuje on przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; należy zachować ten symulator uruchomiony.

   W katalogu `azure-iot-sdk-c` Uruchom następujące polecenie, aby uruchomić symulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Korzystając z programu Visual Studio, Otwórz rozwiązanie wygenerowane w katalogu `cmake` o nazwie `azure_iot_sdks.sln`i skompiluj je za pomocą polecenia **Kompiluj rozwiązanie** w menu **kompilacja** .

1. W okienku **Eksplorator rozwiązań** w programie Visual Studio przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**.

1. Uruchom rozwiązanie przy użyciu dowolnego polecenia **Uruchom** z menu **debugowanie** . W oknie danych wyjściowych zostanie wyświetlony **Identyfikator rejestracji** symulatora modułu TPM i **Klucz poręczenia**, który należy skopiować do użycia później podczas tworzenia rejestracji indywidualnej dla urządzenia w tym oknie (z identyfikatorem rejestracji i kluczem poręczenia), ale pozostawić okno symulatora modułu TPM uruchomione.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Pobieranie informacji o aprowizacji z urządzenia fizycznego

Na urządzeniu Utwórz narzędzie, za pomocą którego można pobrać informacje o aprowizacji urządzenia.

1. Wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) , aby zainstalować i skompilować zestaw SDK urządzeń Azure IoT dla języka C.

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizacji urządzenia z urządzenia TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Skopiuj wartości **Identyfikator rejestracji** i **Klucz poręczenia**. Użyjesz tych wartości do tworzenia rejestracji indywidualnej dla urządzenia w usłudze DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Można postępować zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji usługi DPS

Pobieranie informacji o udostępnianiu z maszyny wirtualnej i używać go do tworzenia rejestracji indywidualnej w usłudze Device Provisioning. 

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki, potrzebne do rozwiązania, takie jak region, środowisko, lokalizacji lub urządzeń typu. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md). 

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service. 

2. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**. 

3. Wybierz pozycję **Dodaj rejestrację indywidualną** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W obszarze **mechanizm**wybierz pozycję **TPM**. 

   2. Podaj **Klucz poręczenia** i **Identyfikator rejestracji** skopiowane z maszyny wirtualnej.

      > [!TIP]
      > W przypadku korzystania z fizycznego urządzenia TPM należy określić **Klucz poręczenia**, który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany z przypisanego do niego producenta mikroukładu modułu TPM. Można utworzyć unikatowy **Identyfikator rejestracji** dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.

   3. Wybierz **wartość true** , aby zadeklarować, że ta maszyna wirtualna jest urządzeniem IoT Edge. 

   4. Wybierz połączone **IoT Hub** , do których chcesz połączyć urządzenie. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji. 

   5. Jeśli chcesz, podaj identyfikator urządzenia. Identyfikatory urządzeń można użyć pod kątem poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.

   6. Jeśli chcesz, Dodaj wartość tagu do **początkowego stanu dwuosiowego urządzenia** . Tagi do docelowych grup urządzeń można użyć do wdrożenia modułu. Na przykład: 

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

   7. Wybierz pozycję **Zapisz**. 

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji. 

## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i pozwalają wdrożyć dodatkowe kontenery na urządzeniu, aby uruchomić kod, na urządzeniach brzegowych. Na maszynie wirtualnej, należy zainstalować środowisko uruchomieniowe usługi IoT Edge. 

Przed rozpoczęciem artykułu, który jest zgodny z typem urządzenia, należy znać **zakres identyfikatorów** DPS i **Identyfikator rejestracji** urządzenia. Jeśli zainstalowano przykładowy serwer Ubuntu, należy użyć instrukcji **x64** . Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi. 

[Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj dostęp do usługi IoT Edge modułu TPM

Aby środowisko uruchomieniowe usługi IoT Edge automatycznie aprowizować urządzenia musi mieć dostęp do modułu TPM. 

Aby zapewnić dostęp do IoT Edge środowiska uruchomieniowego TPM, można zastępowanie ustawień systemowych, aby usługa **iotedge** miała uprawnienia główne. Jeśli nie chcesz do podniesienia uprawnień usługi umożliwia także następujące kroki ręcznie zapewnienie dostępu modułu TPM. 

1. Znajdź ścieżkę pliku do sprzętowego modułu TPM na twoim urządzeniu i zapisz go jako zmienna lokalna. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Utwórz nową regułę, która zapewni usługi IoT Edge dostęp do środowiska uruchomieniowego do tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otwórz plik reguł. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Skopiuj następujące informacje dostępu do pliku reguł. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Zapisz i zamknij plik. 

6. Wyzwalanie systemu udev, aby obliczyć nową regułę. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Sprawdź, czy reguła została pomyślnie zastosowana.

   ```bash
   ls -l /dev/tpm0
   ```

   Pomyślne dane wyjściowe wyglądają następująco:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Jeśli nie widzisz, że zastosowano odpowiednich uprawnień, spróbuj wykonać ponowny rozruch komputera, aby odświeżyć udev. 

## <a name="restart-the-iot-edge-runtime"></a>Uruchom ponownie środowisko uruchomieniowe usługi IoT Edge

Uruchom ponownie środowisko uruchomieniowe usługi IoT Edge, aby pobierał wszystkie zmiany konfiguracji wprowadzone na urządzeniu. 

   ```bash
   sudo systemctl restart iotedge
   ```

Sprawdź, czy środowisko uruchomieniowe usługi IoT Edge jest uruchomiona. 

   ```bash
   sudo systemctl status iotedge
   ```

Jeśli widzisz błędy aprowizacji, może to być, że zmiany konfiguracji nie zostały jeszcze uwzględnione. Spróbuj ponownie uruchomić ponownie demona usługi IoT Edge. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Lub ponowne uruchomienie Twojej maszyny wirtualnej, aby zobaczyć, jeśli zmiany zostały wprowadzone na rozpoczęcie od nowa. 

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko wykonawcze został uruchomiony pomyślnie, możesz przejść do Centrum IoT Hub i zobacz, że nowe urządzenie automatycznie została zainicjowana. Teraz urządzenie jest gotowe do uruchomienia moduły usługi IoT Edge. 

Sprawdź stan demona usługi IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdź dzienniki demona.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Utwórz listę uruchomionych modułów.

```cmd/sh
iotedge list
```

Możesz sprawdzić, czy została użyta Rejestracja indywidualna utworzona w usłudze Device Provisioning. Przejdź do wystąpienia usługi Device Provisioning w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że jest **przypisany** stan rejestracji i identyfikator urządzenia jest wyświetlany. 

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md).
