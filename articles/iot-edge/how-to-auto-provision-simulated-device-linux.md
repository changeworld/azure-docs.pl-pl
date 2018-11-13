---
title: Automatyczna obsługa usługi Azure IoT Edge urządzenia przy użyciu usługi DPS - Linux | Dokumentacja firmy Microsoft
description: Użyj symulowanego modułu TPM na maszynie Wirtualnej systemu Linux do testowania, inicjowanie obsługi administracyjnej urządzeń Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 703dedc69e491377ce0890610a2882ab95ae6e5a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565075"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Tworzenie i aprowizowanie urządzenia usługi Edge z wirtualnego TPM na maszynie wirtualnej systemu Linux

Urządzenia w usłudze Azure IoT Edge mogą być automatycznie aprowizowane za pomocą [usługi Device Provisioning](../iot-dps/index.yml) podobnie jak w przypadku urządzeń, których nie włączono usługi edge. Jeśli znasz procesu automatycznego aprowizowania, zapoznaj się z [automatycznej aprowizacji pojęcia](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem. 

W tym artykule pokazano, jak przetestować automatycznej aprowizacji na symulowanym urządzeniu usługi Edge wykonując następujące kroki: 

* Utwórz maszynę wirtualną systemu Linux (VM) w funkcji Hyper-V za pomocą symulowanego Trusted Platform Module (TPM) dotyczące zabezpieczeń sprzętowych.
* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzyć rejestrację indywidualną dla urządzenia
* Zainstalować środowisko uruchomieniowe usługi IoT Edge i połączyć to urządzenie do Centrum IoT Hub

Kroki opisane w tym artykule są przeznaczone do celów testowych.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputera deweloperskiego z systemem Windows przy użyciu [funkcji Hyper-V włączono](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule używany jest system Windows 10 uruchomionej maszyny Wirtualnej systemu Ubuntu Server. 
* Aktywnym Centrum IoT. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Utwórz maszynę wirtualną systemu Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną systemu Linux na funkcji Hyper-V, który ma symulowanego modułu TPM, aby używać go do testowania, automatycznego aprowizowania współdziałania z usługą IoT Edge. 

### <a name="create-a-virtual-switch"></a>Utwórz przełącznik wirtualny

Przełącznik wirtualny pozwala połączyć się z siecią fizyczną, do maszyny wirtualnej.

1. Otwórz Hyper-V na komputerze z systemem windows. 

2. W **akcje** menu, wybierz opcję **Menedżera przełącznika wirtualnego**. 

3. Wybierz **zewnętrznych** wirtualnego przełącznika, a następnie wybierz **Utwórz przełącznik wirtualny**. 

4. Nazwij nowy przełącznik wirtualny, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia jest ustawiona na **sieci zewnętrznej**, a następnie wybierz **Ok**.

5. Okno podręczne wyświetli ostrzeżenie, może zostać przerwane połączenie sieciowe. Wybierz **tak** aby kontynuować. 

Jeśli widzisz błędy podczas tworzenia nowego przełącznika wirtualnego, upewnij się, że żadne inne przełączniki korzystają karta ethernet i że żadne inne przełączniki, jak używać tej samej nazwie. 

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i zapisać je lokalnie. Na przykład [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Ponownie otwórz funkcji Hyper-V. W **akcje** menu, wybierz opcję **New** > **maszyny wirtualnej**.

3. Wykonaj **Kreatora nowej maszyny wirtualnej** o następujących określonej konfiguracji:

   1. **Określanie generacji**: Wybierz **generacji 2**.
   2. **Konfigurowanie sieci**: Ustaw wartość **połączenia** do przełącznika wirtualnego, który został utworzony w poprzedniej sekcji. 
   3. **Opcje instalacji**: Wybierz **zainstalować system operacyjny z pliku obrazu rozruchowego** i przejdź do pliku obrazu dysku, który został zapisany lokalnie.

Może upłynąć minut widok, aby utworzyć nową maszynę Wirtualną. 

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

1. Po utworzeniu maszyny Wirtualnej, otwórz jej ustawienia. 
2. Przejdź do **zabezpieczeń**. 
3. Usuń zaznaczenie pola wyboru **włączyć funkcję Bezpieczny rozruch**.
4. Sprawdź **Włącz Trusted Platform Module**. 
5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchom maszynę wirtualną i zbieranie danych modułu TPM

Na maszynie wirtualnej kompilacji narzędzi zestawu SDK języka C, używanej do pobierania urządzenia **identyfikator rejestracji** i **klucz poręczenia**. 

1. Uruchom maszynę Wirtualną i nawiązać z nim, aby zakończyć proces instalacji. 

2. Na maszynie wirtualnej, wykonaj kroki opisane w [skonfiguruj środowisko projektowe systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) do zainstalowania i Skompiluj zestaw SDK urządzeń Azure IoT dla języka C. 

3. Uruchom następujące polecenia, aby tworzyć narzędzia zestawu SDK języka C, która pobiera dane inicjowania obsługi administracyjnej urządzeń. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. Skopiuj wartości pól **identyfikator rejestracji** i **klucz poręczenia**. Użyjesz tych wartości do tworzenia rejestracji indywidualnej dla urządzenia w usłudze DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Możesz wykonać instrukcje w [Konfigurowanie usługi IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po użytkownik ma uruchomione usługi Device Provisioning, skopiuj wartość **zakres identyfikatorów** na stronie Przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji usługi DPS

Pobieranie informacji o udostępnianiu z maszyny wirtualnej i używać go do tworzenia rejestracji indywidualnej w usłudze Device Provisioning. 

Po utworzeniu rejestracji w usłudze DPS mają okazję do deklarowania **początkowy stan bliźniaczej reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki potrzebnych w rozwiązaniu, takich jak typ region, środowisko, lokalizacji lub urządzenia. Tagi te są używane do tworzenia [automatycznego wdrożenia](how-to-deploy-monitor.md). 


1. W [witryny Azure portal](https://portal.azure.com), a następnie przejdź do swojego wystąpienia IoT Hub Device Provisioning Service. 

2. W obszarze **ustawienia**, wybierz opcję **Zarządzanie rejestracjami**. 

3. Wybierz **Dodaj rejestrację indywidualną** następnie wykonaj następujące kroki, aby skonfigurować rejestrowanie:  

   1. Aby uzyskać **mechanizm**, wybierz opcję **modułu TPM**. 
   2. Wstaw **klucz poręczenia** i **identyfikator rejestracji** skopiowaną z maszyny wirtualnej.
   3. Wybierz **Włącz** Aby zadeklarować, że ta maszyna wirtualna jest urządzenia usługi IoT Edge. 
   4. Wybierz połączoną **usługi IoT Hub** chcesz połączyć z urządzeniem, aby. 
   5. Jeśli chcesz, podaj identyfikator urządzenia. Identyfikatory urządzeń można użyć pod kątem poszczególnych urządzeń do wdrożenia modułu. 
   6. Dodaj wartość tagu **początkowy stan bliźniaczej reprezentacji urządzenia** Jeśli chcesz. Tagi do docelowych grup urządzeń można użyć do wdrożenia modułu. 
   7. Wybierz pozycję **Zapisz**. 


## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i pozwalają wdrożyć dodatkowe kontenery na urządzeniu, aby uruchomić kod, na urządzeniach brzegowych. Na maszynie wirtualnej, należy zainstalować środowisko uruchomieniowe usługi IoT Edge. 

Znasz usłudze DPS **zakres identyfikatorów** i urządzenia **identyfikator rejestracji** przed rozpoczęciem tego artykułu, który odpowiada danemu typowi urządzenia. Jeśli zainstalowano przykładowe Ubuntu server, użyj **x64** instrukcje. Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi. 

* [Linux (x64)](how-to-install-iot-edge-linux.md)
* [Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj dostęp do usługi IoT Edge modułu TPM

Aby środowisko uruchomieniowe usługi IoT Edge automatycznie aprowizować urządzenia musi mieć dostęp do modułu TPM. 

Wykonaj następujące kroki, aby udzielić dostępu modułu TPM. Alternatywnie, można osiągnąć to samo przez zastąpienie ustawień systemd, aby *iotedge* usługa może działać jako użytkownik główny. 

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
   crw------- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

8. Otwórz plik zastępuje środowiska uruchomieniowego usługi IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Dodaj następujący kod, aby ustanowić zmienną środowiskową modułu TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. Sprawdź, czy zastąpienie zakończyła się pomyślnie.

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

Jeśli widzisz błędy aprowizacji, może to być, że zmiany konfiguracji nie zostały jeszcze uwzględnione. Spróbuj ponownie uruchomić demona usługi IoT Edge uzyskać. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Lub ponowne uruchomienie Twojej maszyny wirtualnej, aby zobaczyć, jeśli zmiany zostały wprowadzone na rozpoczęcie od nowa. 

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko wykonawcze został uruchomiony pomyślnie, możesz przejść do Centrum IoT Hub i czy nowe urządzenie automatycznie została przygotowana i jest gotowy do uruchomienia moduły usługi IoT Edge. 

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


## <a name="next-steps"></a>Kolejne kroki

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [monitora usługi IoT Edge modułów na skalowanie przy użyciu witryny Azure portal i wdróż](how-to-deploy-monitor.md) lub [przy użyciu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)
