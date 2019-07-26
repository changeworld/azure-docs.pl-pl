---
title: Zainicjuj obsługę administracyjną urządzeń za pomocą usługi DPS z zaświadczeniem klucza symetrycznego — Azure IoT Edge | Microsoft Docs
description: Korzystanie z zaświadczania klucza symetrycznego do testowania automatycznej aprowizacji urządzeń dla Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c21c0bdce6f6a5cd3c8f634bf400600b30a8ead
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414594"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu zaświadczania klucza symetrycznego

Urządzenia w usłudze Azure IoT Edge mogą być autoprovisioned przy użyciu [usługi Device Provisioning](../iot-dps/index.yml) podobnie jak w przypadku urządzeń, których nie włączono usługi edge. Jeśli znasz procesu autoprovisioning, zapoznaj się z [pojęcia autoprovisioning](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem.

W tym artykule opisano sposób tworzenia rejestracji indywidualnej usługi Device Provisioning przy użyciu zaświadczania klucza symetrycznego na urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzyć rejestrację indywidualną dla urządzenia.
* Zainstaluj środowisko uruchomieniowe IoT Edge i Połącz się z IoT Hub.

Zaświadczenie klucza symetrycznego to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia usługi Device Provisioning. Ta metoda zaświadczania reprezentuje środowisko "Hello World" dla deweloperów, którzy są nowym sposobem aprowizacji urządzeń lub nie mają rygorystycznych wymagań dotyczących zabezpieczeń. Zaświadczanie urządzenia przy użyciu [modułu TPM](../iot-dps/concepts-tpm-attestation.md) jest bezpieczniejsze i powinno być używane na potrzeby bardziej rygorystycznych wymagań dotyczących zabezpieczeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywna IoT Hub
* Urządzenie fizyczne lub wirtualne

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Możesz wykonać instrukcje w [Konfigurowanie usługi IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po użytkownik ma uruchomione usługi Device Provisioning, skopiuj wartość **zakres identyfikatorów** na stronie Przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybierz unikatowy identyfikator rejestracji dla urządzenia

Unikatowy identyfikator rejestracji musi być zdefiniowany, aby można było zidentyfikować każde urządzenie. Możesz użyć adresu MAC, numeru seryjnego lub wszelkich unikatowych informacji z urządzenia.

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego tworzącego następujący ciąg dla identyfikatora rejestracji.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Utwórz unikatowy identyfikator rejestracji dla urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i myślnik ("-").

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji usługi DPS

Użyj identyfikatora rejestracji urządzenia, aby utworzyć rejestrację indywidualną w usłudze DPS.

Po utworzeniu rejestracji w usłudze DPS mają okazję do deklarowania **początkowy stan bliźniaczej reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki, potrzebne do rozwiązania, takie jak region, środowisko, lokalizacji lub urządzeń typu. Tagi te są używane do tworzenia [automatycznego wdrożenia](how-to-deploy-monitor.md).

> [!TIP]
> Rejestracje grup są również możliwe podczas korzystania z zaświadczania klucza symetrycznego i obejmują te same decyzje co rejestracje indywidualne.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

1. W obszarze **ustawienia**, wybierz opcję **Zarządzanie rejestracjami**.

1. Wybierz **Dodaj rejestrację indywidualną** następnie wykonaj następujące kroki, aby skonfigurować rejestrowanie:  

   1. W obszarze **mechanizm**wybierz opcję **klucz symetryczny**.

   1. Zaznacz pole wyboru **automatycznie Generuj klucze** .

   1. Podaj **Identyfikator rejestracji** , który został utworzony dla Twojego urządzenia.

   1. Podaj **Identyfikator urządzenia IoT Hub** , jeśli chcesz. Identyfikatory urządzeń można użyć pod kątem poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.

   1. Wybierz **wartość true** , aby zadeklarować, że rejestracja dotyczy urządzenia IoT Edge. W przypadku rejestracji grupy wszystkie urządzenia muszą być IoT Edge urządzeń lub żadna z nich nie może być.

   1. Zaakceptuj wartość domyślną z zasad alokacji usługi Device Provisioning, aby określić **sposób przypisywania urządzeń do centrów** lub wybrać inną wartość specyficzną dla tej rejestracji.

   1. Wybierz połączoną **usługi IoT Hub** chcesz połączyć z urządzeniem, aby. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji.

   1. Wybierz **, w jaki sposób dane urządzenia mają być obsługiwane na potrzeby ponownej aprowizacji,** gdy urządzenia zażądają aprowizacji po raz pierwszy.

   1. Dodaj wartość tagu **początkowy stan bliźniaczej reprezentacji urządzenia** Jeśli chcesz. Tagi do docelowych grup urządzeń można użyć do wdrożenia modułu. Na przykład:

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

   1. Upewnij się, że **wpis Enable** ma wartość **enable**.

   1. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji. Należy pamiętać, aby skopiować wartość **klucza podstawowego** rejestracji do użycia podczas tworzenia klucza urządzenia.

## <a name="derive-a-device-key"></a>Utwórz klucz urządzenia

Urządzenie używa pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji w celu przeprowadzenia zaświadczania klucza symetrycznego z rejestracją podczas aprowizacji. Aby wygenerować klucz urządzenia, użyj klucza skopiowanego z rejestracji usługi DPS w celu obliczenia [algorytmu HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji dla urządzenia i Przekształć wynik w formacie base64.

Nie uwzględniaj klucza podstawowego lub pomocniczego rejestracji w kodzie urządzenia.

### <a name="linux-workstations"></a>Stacje robocze systemu Linux

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracji urządzenia.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Stacje robocze z systemem Windows

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracji urządzenia.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i pozwalają wdrożyć dodatkowe kontenery na urządzeniu, aby uruchomić kod, na urządzeniach brzegowych.

Podczas aprowizacji urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Utworzony **Identyfikator rejestracji** urządzenia
* Klucz urządzenia pochodnego urządzenia na potrzeby zaświadczania klucza symetrycznego

### <a name="linux-device"></a>Urządzenie z systemem Linux

Postępuj zgodnie z instrukcjami dotyczącymi architektury urządzenia. Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi.

[Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

Sekcja w pliku konfiguracyjnym dla udostępniania klucza symetrycznego wygląda następująco:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Zastąp wartości symboli zastępczych `{registration_id}`dla `{scope_id}`, `{symmetric_key}` i z danymi, które zostały zebrane wcześniej.

### <a name="windows-device"></a>Urządzenie z systemem Windows

Postępuj zgodnie z instrukcjami, aby zainstalować środowisko uruchomieniowe IoT Edge na urządzeniu, dla którego Wygenerowano pochodny klucz urządzenia. Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi.

[Instalowanie i automatyczne Inicjowanie obsługi IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko wykonawcze został uruchomiony pomyślnie, możesz przejść do Centrum IoT Hub i zacząć wdrażać moduły usługi IoT Edge na urządzeniu. Użyj następujących poleceń na twoim urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zainstalowana i uruchomiona pomyślnie.

### <a name="linux-device"></a>Urządzenie z systemem Linux

Sprawdź stan usługi IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdzanie dzienników usług.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Utwórz listę uruchomionych modułów.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Urządzenie z systemem Windows

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdzanie dzienników usług.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Utwórz listę uruchomionych modułów.

```powershell
iotedge list
```

Możesz sprawdzić, czy została użyta Rejestracja indywidualna utworzona w usłudze Device Provisioning. Przejdź do wystąpienia usługi Device Provisioning w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że jest **przypisany** stan rejestracji i identyfikator urządzenia jest wyświetlany.

## <a name="next-steps"></a>Kolejne kroki

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [monitora usługi IoT Edge modułów na skalowanie przy użyciu witryny Azure portal i wdróż](how-to-deploy-monitor.md) lub [przy użyciu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md).
