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
ms.custom: seodec18
ms.openlocfilehash: b48455b6ea9c1cd74e94c10d8f9f938c20512c02
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414580"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux

Azure IoT Edge urządzeń można automatycznie zainicjować przy użyciu [usługi Device](../iot-dps/index.yml)Provisioning. Jeśli znasz procesu autoprovisioning, zapoznaj się z [pojęcia autoprovisioning](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem. 

W tym artykule pokazano, jak przetestować autoudostępnianie na symulowanym urządzeniu IoT Edge, wykonując następujące czynności: 

* Utwórz maszynę wirtualną systemu Linux (VM) w funkcji Hyper-V za pomocą symulowanego Trusted Platform Module (TPM) dotyczące zabezpieczeń sprzętowych.
* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzyć rejestrację indywidualną dla urządzenia
* Zainstalować środowisko uruchomieniowe usługi IoT Edge i połączyć to urządzenie do Centrum IoT Hub

Kroki opisane w tym artykule są przeznaczone do celów testowych.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputera deweloperskiego z systemem Windows przy użyciu [funkcji Hyper-V włączono](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule używany jest system Windows 10 uruchomionej maszyny Wirtualnej systemu Ubuntu Server. 
* Aktywnym Centrum IoT. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Utwórz maszynę wirtualną systemu Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną z systemem Linux w ramach funkcji Hyper-V. Ta maszyna wirtualna została skonfigurowana przy użyciu symulowanego modułu TPM, aby można było użyć jej do testowania, w jaki sposób Automatyczna obsługa administracyjna współpracuje z IoT Edge. 

### <a name="create-a-virtual-switch"></a>Utwórz przełącznik wirtualny

Przełącznik wirtualny pozwala połączyć się z siecią fizyczną, do maszyny wirtualnej.

1. Otwórz Menedżera funkcji Hyper-V na komputerze z systemem Windows. 

2. W **akcje** menu, wybierz opcję **Menedżera przełącznika wirtualnego**. 

3. Wybierz **zewnętrznych** wirtualnego przełącznika, a następnie wybierz **Utwórz przełącznik wirtualny**. 

4. Nazwij nowy przełącznik wirtualny, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia jest ustawiona na **sieci zewnętrznej**, a następnie wybierz **Ok**.

5. Okno podręczne wyświetli ostrzeżenie, może zostać przerwane połączenie sieciowe. Wybierz **tak** aby kontynuować. 

Jeśli widzisz błędy podczas tworzenia nowego przełącznika wirtualnego, upewnij się, że żadne inne przełączniki korzystają karta ethernet i że żadne inne przełączniki, jak używać tej samej nazwie. 

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i zapisać je lokalnie. Na przykład [Ubuntu server](https://www.ubuntu.com/download/server). 

2. W Menedżerze funkcji Hyper-V ponownie wybierz pozycję **Nowa** > **maszyna wirtualna** w menu **Akcje** .

3. Wykonaj **Kreatora nowej maszyny wirtualnej** o następujących określonej konfiguracji:

   1. **Określ generację**: Wybierz pozycję **generacja 2**. Maszyny wirtualne generacji 2 mają włączoną funkcję wirtualizacji zagnieżdżonej, która jest wymagana do uruchamiania IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci**: Ustaw wartość ustawienia **połączenie** z przełącznikiem wirtualnym, który został utworzony w poprzedniej sekcji. 
   3. **Opcje instalacji**: Wybierz opcję **Zainstaluj system operacyjny z pliku obrazu** rozruchowego i przejdź do pliku obrazu dysku zapisanego lokalnie.

4. Wybierz pozycję **Zakończ** w kreatorze, aby utworzyć maszynę wirtualną.

Może upłynąć kilka minut na utworzenie nowej maszyny Wirtualnej. 

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

Po utworzeniu maszyny wirtualnej Otwórz jej ustawienia, aby włączyć moduł TPM (Trusted Platform Module), który umożliwia samoobsługowe Inicjowanie obsługi urządzenia. 

1. Wybierz maszynę wirtualną, a następnie otwórz jej **Ustawienia**.

2. Przejdź do **zabezpieczeń**. 

3. Usuń zaznaczenie pola wyboru **włączyć funkcję Bezpieczny rozruch**.

4. Sprawdź **Włącz Trusted Platform Module**. 

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchom maszynę wirtualną i zbieranie danych modułu TPM

Na maszynie wirtualnej kompilacji narzędzi zestawu SDK języka C, używanej do pobierania urządzenia **identyfikator rejestracji** i **klucz poręczenia**. 

1. Uruchom maszynę wirtualną i nawiąż z nią połączenie.

2. Postępuj zgodnie z monitami na maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić maszynę. 

3. Zaloguj się do maszyny wirtualnej, a następnie wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i SKOMPILOWANIA zestawu SDK urządzeń Azure IoT dla języka C. 

   >[!TIP]
   >W tym artykule opisano kopiowanie do i wklejanie z maszyny wirtualnej, co nie jest łatwe w aplikacji połączenia Menedżera funkcji Hyper-V. Aby pobrać swój adres IP, możesz połączyć się z maszyną wirtualną za poorednictwem Menedżera funkcji Hyper-V `ifconfig`:. Następnie można użyć adresu IP, aby nawiązać połączenie za pośrednictwem `ssh <username>@<ipaddress>`protokołu SSH:.

4. Uruchom następujące polecenia, aby tworzyć narzędzia zestawu SDK języka C, która pobiera dane inicjowania obsługi administracyjnej urządzeń. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >W przypadku testowania przy użyciu symulatora modułu TPM należy umieścić dodatkowy parametr `-Duse_tpm_simulator:BOOL=ON` , aby go włączyć. Pełne polecenie będzie miało `cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..`wartość.

5. Skopiuj wartości pól **identyfikator rejestracji** i **klucz poręczenia**. Użyjesz tych wartości do tworzenia rejestracji indywidualnej dla urządzenia w usłudze DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Możesz wykonać instrukcje w [Konfigurowanie usługi IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po użytkownik ma uruchomione usługi Device Provisioning, skopiuj wartość **zakres identyfikatorów** na stronie Przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji usługi DPS

Pobieranie informacji o udostępnianiu z maszyny wirtualnej i używać go do tworzenia rejestracji indywidualnej w usłudze Device Provisioning. 

Po utworzeniu rejestracji w usłudze DPS mają okazję do deklarowania **początkowy stan bliźniaczej reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki, potrzebne do rozwiązania, takie jak region, środowisko, lokalizacji lub urządzeń typu. Tagi te są używane do tworzenia [automatycznego wdrożenia](how-to-deploy-monitor.md). 

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service. 

2. W obszarze **ustawienia**, wybierz opcję **Zarządzanie rejestracjami**. 

3. Wybierz **Dodaj rejestrację indywidualną** następnie wykonaj następujące kroki, aby skonfigurować rejestrowanie:  

   1. Aby uzyskać **mechanizm**, wybierz opcję **modułu TPM**. 
   
   2. Podaj **Klucz poręczenia** i **Identyfikator rejestracji** skopiowane z maszyny wirtualnej.
   
   3. Wybierz **wartość true** , aby zadeklarować, że ta maszyna wirtualna jest urządzeniem IoT Edge. 
   
   4. Wybierz połączoną **usługi IoT Hub** chcesz połączyć z urządzeniem, aby. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji. 
   
   5. Jeśli chcesz, podaj identyfikator urządzenia. Identyfikatory urządzeń można użyć pod kątem poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.
   
   6. Dodaj wartość tagu **początkowy stan bliźniaczej reprezentacji urządzenia** Jeśli chcesz. Tagi do docelowych grup urządzeń można użyć do wdrożenia modułu. Na przykład: 

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

Znasz usłudze DPS **zakres identyfikatorów** i urządzenia **identyfikator rejestracji** przed rozpoczęciem tego artykułu, który odpowiada danemu typowi urządzenia. Jeśli zainstalowano przykładowe Ubuntu server, użyj **x64** instrukcje. Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi. 

[Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj dostęp do usługi IoT Edge modułu TPM

Aby środowisko uruchomieniowe usługi IoT Edge automatycznie aprowizować urządzenia musi mieć dostęp do modułu TPM. 

Możesz udzielić dostępu modułu TPM do środowiska uruchomieniowego usługi IoT Edge przez zastąpienie ustawień systemd, aby **iotedge** usługa ma uprawnień użytkownika root. Jeśli nie chcesz do podniesienia uprawnień usługi umożliwia także następujące kroki ręcznie zapewnienie dostępu modułu TPM. 

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

8. Otwórz plik zastępuje środowiska uruchomieniowego usługi IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Dodaj następujący kod, aby ustanowić zmienną środowiskową modułu TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Zapisz i zamknij plik.

11. Sprawdź, czy zastąpienie zakończyła się pomyślnie.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    Pomyślne dane wyjściowe wyświetla **iotedge** domyślne zmienne usługę i następnie pokazuje zmiennej środowiskowej, można ustawić w **override.conf**. 

12. Załaduj ponownie ustawienia.

    ```bash
    sudo systemctl daemon-reload
    ```

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

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [monitora usługi IoT Edge modułów na skalowanie przy użyciu witryny Azure portal i wdróż](how-to-deploy-monitor.md) lub [przy użyciu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md).
