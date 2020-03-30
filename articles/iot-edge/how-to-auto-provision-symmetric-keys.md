---
title: Udostępnianie urządzenia przy użyciu zaświadczania klucza symetrycznego — usługa Azure IoT Edge
description: Użyj zaświadczania klucza symetrycznego, aby przetestować automatyczne inicjowanie obsługi administracyjnej urządzeń dla usługi Azure IoT Edge z usługą inicjowania obsługi administracyjnej urządzeń
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535922"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Tworzenie i inicjowania obsługi administracyjnej urządzenia usługi IoT Edge przy użyciu zaświadczania klucza symetrycznego

Urządzenia usługi Azure IoT Edge mogą być automatycznie aprowizacji przy użyciu [usługi inicjowania obsługi administracyjnej urządzeń,](../iot-dps/index.yml) podobnie jak urządzenia, które nie są włączone krawędzi. Jeśli nie znasz procesu automatycznego inicjowania obsługi administracyjnej, przejrzyj [pojęcia automatycznego inicjowania obsługi administracyjnej](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem.

W tym artykule pokazano, jak utworzyć indywidualną rejestrację usługi inicjowania obsługi administracyjnej urządzeń przy użyciu zaświadczania klucza symetrycznego na urządzeniu usługi IoT Edge, wykonując następujące czynności:

* Utwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzeń (DPS) centrum IoT.
* Utwórz indywidualną rejestrację dla urządzenia.
* Zainstaluj środowisko uruchomieniowe usługi IoT Edge i połącz się z centrum IoT Hub.

Zaświadczanie klucza symetrycznego jest proste podejście do uwierzytelniania urządzenia z wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Ta metoda zaświadczania reprezentuje środowisko "Hello world" dla deweloperów, którzy są nowicjuszami w inicjowaniu obsługi administracyjnej urządzeń lub nie mają ścisłych wymagań dotyczących zabezpieczeń. Zaświadczanie urządzenia przy użyciu certyfikatów [modułu TPM](../iot-dps/concepts-tpm-attestation.md) lub [X.509](../iot-dps/concepts-security.md#x509-certificates) jest bezpieczniejsze i powinno być używane do bardziej rygorystycznych wymagań dotyczących zabezpieczeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne centrum IoT
* Urządzenie fizyczne lub wirtualne

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub

Utwórz nowe wystąpienie usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi Usługi IoT na platformie Azure i połącz je z centrum IoT hub. Postępuj zgodnie z instrukcjami w [konfigurowaniu dps centrum IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po uruchomieniu usługi inicjowania obsługi administracyjnej urządzeń skopiuj wartość **zakresu identyfikatora** ze strony przeglądowej. Ta wartość jest używana podczas konfigurowania środowiska wykonawczego usługi IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybierz unikatowy identyfikator rejestracyjny urządzenia

Unikatowy identyfikator rejestracji musi być zdefiniowany w celu zidentyfikowania każdego urządzenia. Z urządzenia można użyć adresu MAC, numeru seryjnego lub unikatowych informacji.

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego tworzącego następujący `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`ciąg dla identyfikatora rejestracji: .

Utwórz unikatowy identyfikator rejestracyjny urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i myślnik ('-').

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji DPS

Użyj identyfikatora rejestracji urządzenia, aby utworzyć indywidualną rejestrację w dps.

Podczas tworzenia rejestracji w dps, masz możliwość zadeklarowania **stanu bliźniaczej reprezentacji urządzenia początkowego**. W bliźniaczej reprezentacji urządzenia można ustawić znaczniki, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, takiej jak region, środowisko, lokalizacja lub typ urządzenia. Tagi te są używane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

> [!TIP]
> Rejestracje grupowe są również możliwe podczas korzystania z zaświadczania klucza symetrycznego i wiążą się z tymi samymi decyzjami co rejestracje indywidualne.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

1. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz **pozycję Dodaj rejestrację indywidualną,** a następnie wykonaj następujące czynności, aby skonfigurować rejestrację:  

   1. W przypadku **opcji Mechanizm**wybierz opcję **Klucz symetryczny**.

   1. Zaznacz pole wyboru **Automatyczne generowanie kluczy.**

   1. Podaj **identyfikator rejestracji** utworzony dla urządzenia.

   1. Jeśli chcesz, podaj identyfikator **urządzenia usługi IoT Hub** dla swojego urządzenia. Identyfikatory urządzeń można użyć do kierowania poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podasz identyfikatora urządzenia, zostanie użyty identyfikator rejestracji.

   1. Wybierz **wartość True,** aby zadeklarować, że rejestracja dotyczy urządzenia ZoT. W przypadku rejestracji w grupie wszystkie urządzenia muszą być urządzeniami IoT Edge lub żadne z nich nie może być.

   1. Zaakceptuj wartość domyślną z zasad alokacji usługi inicjowania obsługi administracyjnej urządzeń, **aby przypisać urządzenia do koncentratorów** lub wybrać inną wartość specyficzną dla tej rejestracji.

   1. Wybierz połączone centrum **IoT Hub,** do którego chcesz podłączyć urządzenie. Można wybrać wiele koncentratorów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   1. Wybierz **sposób obsługi danych urządzenia podczas ponownej inicjowania obsługi administracyjnej,** gdy urządzenia żądają inicjowania obsługi administracyjnej po raz pierwszy.

   1. Dodaj wartość znacznika do **stanu bliźniaczej reprezentacji urządzenia początkowego,** jeśli chcesz. Tagów można używać do grup docelowych urządzeń do wdrażania modułu. Przykład:

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

   1. Upewnij **się,** że włącz wpis jest ustawiony na **Włącz**.

   1. Wybierz **pozycję Zapisz**.

Teraz, gdy istnieje rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie aprowizować urządzenie podczas instalacji. Pamiętaj, aby skopiować wartość **klucza podstawowego** rejestracji do użycia podczas instalowania środowiska wykonawczego usługi IoT Edge lub jeśli zamierzasz tworzyć klucze urządzenia do użytku z rejestracją grupy.

## <a name="derive-a-device-key"></a>Wyprowadzanie klucza urządzenia

> [!NOTE]
> Ta sekcja jest wymagana tylko w przypadku korzystania z rejestracji grupy.

Każde urządzenie używa swojego pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji do wykonywania zaświadczania klucza symetrycznego z rejestracją podczas inicjowania obsługi administracyjnej. Aby wygenerować klucz urządzenia, użyj klucza skopiowanego z rejestracji DPS, aby obliczyć [identyfikator HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji urządzenia i przekonwertować wynik na format Base64.

Nie należy dołączać klucza podstawowego lub pomocniczego rejestracji do kodu urządzenia.

### <a name="linux-workstations"></a>Stacje robocze Linuksa

Jeśli używasz stacji roboczej systemu Linux, można użyć openssl do wygenerowania klucza urządzenia pochodnego, jak pokazano w poniższym przykładzie.

Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracyjnym urządzenia.

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

Jeśli używasz stacji roboczej opartej na systemie Windows, można użyć programu PowerShell do wygenerowania klucza urządzenia pochodnego, jak pokazano w poniższym przykładzie.

Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracyjnym urządzenia.

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

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska wykonawczego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki są uruchamiane w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchomić kod na krawędzi.

Podczas inicjowania obsługi administracyjnej urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Identyfikator **rejestracji** urządzenia, który został utworzony
* **Klucz podstawowy** skopiowany z rejestracji DPS

> [!TIP]
> W przypadku rejestracji grupowych potrzebny jest [klucz pochodny](#derive-a-device-key) każdego urządzenia, a nie klucz rejestracji DPS.

### <a name="linux-device"></a>Urządzenie z systemem Linux

Postępuj zgodnie z instrukcjami dotyczącymi architektury urządzenia. Upewnij się, aby skonfigurować środowisko uruchomieniowe IoT Edge dla automatycznego, a nie ręcznego inicjowania obsługi administracyjnej.

[Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

Sekcja w pliku konfiguracyjnym dla inicjowania obsługi administracyjnej kluczy symetrycznych wygląda następująco:

```yaml
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

Zastąp wartości `<SCOPE_ID>` `<REGISTRATION_ID>`zastępcze `<SYMMETRIC_KEY>` dla , i danymi zebranymi wcześniej. Upewnij się, że **inicjowanie obsługi administracyjnej:** wiersz nie ma poprzedniego odstępu i że elementy zagnieżdżone są wcięte przez dwie spacje.

### <a name="windows-device"></a>Urządzenie z systemem Windows

Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu, dla którego wygenerowano klucz urządzenia pochodnego. Środowiska wykonawczego IoT Edge można skonfigurować do automatycznego, a nie ręcznego inicjowania obsługi administracyjnej.

Aby uzyskać bardziej szczegółowe informacje dotyczące instalowania usługi IoT Edge w systemie Windows, w tym wymagania wstępne i instrukcje dotyczące zadań, takich jak zarządzanie kontenerami i aktualizowanie usługi IoT Edge, zobacz [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby podczas instalowania aplikacji IoT Edge, a nie programu PowerShell (x86), należy używać sesji programu AMD64 programu PowerShell.

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby i środowisko wykonawcze usługi IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie ponownie uruchomione. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, uruchom program PowerShell jako administrator ponownie.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi `-Dps` administracyjnej za pomocą kontenerów systemu Windows, chyba że używasz flagi do użycia automatycznego inicjowania obsługi administracyjnej.

   Zastąp wartości `{scope_id}` `{registration_id}`zastępcze `{symmetric_key}` dla , i danymi zebranymi wcześniej.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Jeśli środowisko wykonawcze rozpoczęło się pomyślnie, możesz przejść do centrum IoT Hub i rozpocząć wdrażanie modułów usługi IoT Edge na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko wykonawcze zostało zainstalowane i uruchomione pomyślnie.

### <a name="linux-device"></a>Urządzenie z systemem Linux

Sprawdź stan usługi IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdź dzienniki usług.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista uruchomionych modułów.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Urządzenie z systemem Windows

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usług.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista uruchomionych modułów.

```powershell
iotedge list
```

Można sprawdzić, czy została użyta indywidualna rejestracja utworzona w usłudze inicjowania obsługi administracyjnej urządzeń. Przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń w witrynie Azure portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Należy zauważyć, że stan rejestracji jest **przypisany** i identyfikator urządzenia jest wyświetlany.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi inicjowania obsługi administracyjnej urządzeń umożliwia ustawienie identyfikatora urządzenia i bliźniaczych tagów urządzenia w tym samym czasie, co aprowizowanie nowego urządzenia. Za pomocą tych wartości można kierować reklamy na poszczególne urządzenia lub grupy urządzeń za pomocą automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure.](how-to-deploy-monitor-cli.md)
