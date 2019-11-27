---
title: Automatyczne Inicjowanie obsługi urządzeń z systemem Windows przy użyciu programu DPS — Azure IoT Edge | Microsoft Docs
description: Korzystanie z symulowanego urządzenia na urządzeniu Windows do testowania, automatyczne aprowizowanie urządzeń Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457145"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Tworzenie i Inicjowanie obsługi symulowanego urządzenia IoT Edge przy użyciu wirtualnego modułu TPM w systemie Windows

Urządzenia Azure IoT Edge mogą być obsługiwane przy użyciu [usługi Device Provisioning](../iot-dps/index.yml) , podobnie jak dla urządzeń, które nie są włączone. Jeśli nie znasz procesu samoobsługowego inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z [pojęciami autouzupełniania](../iot-dps/concepts-auto-provisioning.md) .

Usługa DPS obsługuje zaświadczenie klucza symetrycznego dla IoT Edge urządzeń zarówno podczas rejestracji indywidualnej, jak i rejestracji grupowej. Jeśli w przypadku rejestracji grupowej opcja "is IoT Edge Device" ma wartość true w zaświadczeniu klucza symetrycznego, wszystkie urządzenia zarejestrowane w ramach tej grupy rejestracji zostaną oznaczone jako urządzenia IoT Edge. 

W tym artykule pokazano, jak przetestować funkcję autoaprowizacji na symulowanym urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzenie symulowanego urządzenia na urządzeniu Windows przy użyciu symulowanego Trusted Platform Module (TPM) dotyczące zabezpieczeń sprzętowych.
* Utworzyć rejestrację indywidualną dla urządzenia.
* Zainstalować środowisko uruchomieniowe usługi IoT Edge i połączyć to urządzenie do Centrum IoT Hub.

> [!NOTE]
> Moduł TPM 2,0 jest wymagany w przypadku korzystania z zaświadczania TPM z usługą DPS i może być używany tylko do tworzenia pojedynczych, nie grup, rejestracji.

> [!TIP]
> W tym artykule opisano testowanie samoobsługowego testowania przy użyciu zaświadczania modułu TPM na urządzeniach wirtualnych, ale większość jest stosowana w przypadku używania fizycznego sprzętu modułu TPM.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputera deweloperskiego z systemem Windows. W tym artykule używany jest system Windows 10.
* Aktywnym Centrum IoT.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Można postępować zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge.

> [!TIP]
> W przypadku korzystania z fizycznego urządzenia TPM należy określić **Klucz poręczenia**, który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany z przypisanego do niego producenta mikroukładu modułu TPM. Można utworzyć unikatowy **Identyfikator rejestracji** dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.
>
> Postępuj zgodnie z instrukcjami w artykule [jak zarządzać rejestracjami urządzeń za pomocą witryny Azure Portal](../iot-dps/how-to-manage-enrollments.md) , aby utworzyć rejestrację w usłudze DPS, a następnie przejdź do sekcji [Zainstaluj środowisko uruchomieniowe IoT Edge](#install-the-iot-edge-runtime) w tym artykule, aby kontynuować.

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Tworzenie symulowanego urządzenia TPM na komputerze deweloperskim Windows. Pobierz **Identyfikator rejestracji** i **Klucz poręczenia** dla urządzenia i użyj go do utworzenia indywidualnego wpisu rejestracji w usłudze DPS.

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki potrzebnych w rozwiązaniu, takich jak typ region, środowisko, lokalizacji lub urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

Wybierz język zestawu SDK, który ma używać do tworzenia symulowanego urządzenia, a następnie postępuj zgodnie z instrukcjami, do czasu utworzenia rejestracji indywidualnej.

Podczas tworzenia rejestracji indywidualnej wybierz **wartość true** , aby zadeklarować, że symulowane urządzenie TPM na komputerze deweloperskim systemu Windows jest **urządzeniem IoT Edge**.

Symulowane urządzenie i przewodniki dotyczące rejestracji indywidualnej:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu rejestracji indywidualnej Zapisz wartość **identyfikatora rejestracji**. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i pozwalają wdrożyć dodatkowe kontenery na urządzeniu, aby uruchomić kod, na urządzeniach brzegowych.

Podczas aprowizacji urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Utworzony **Identyfikator rejestracji** urządzenia

Zainstaluj środowisko uruchomieniowe IoT Edge na urządzeniu, na którym działa symulowany moduł TPM. Skonfigurujesz środowisko uruchomieniowe IoT Edge automatyczne, nie ręczne, Inicjowanie obsługi administracyjnej.

> [!TIP]
> Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania.

Aby uzyskać bardziej szczegółowe informacje na temat instalowania IoT Edge w systemie Windows, w tym wymagania wstępne i instrukcje dotyczące zadań, takich jak Zarządzanie kontenerami i aktualizowanie IoT Edge, zobacz [Instalowanie środowiska uruchomieniowego Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

1. Otwórz okno programu PowerShell w trybie administratora. Należy pamiętać, aby podczas instalowania IoT Edge używać sesji AMD64 programu PowerShell, a nie programu PowerShell (x86).

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows. Użyj flagi `-Dps`, aby użyć usługi Device Provisioning zamiast ręcznego inicjowania obsługi.

   Zamień wartości zastępcze dla `{scope_id}` i `{registration_id}` na zebrane wcześniej dane.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko wykonawcze został uruchomiony pomyślnie, możesz przejść do Centrum IoT Hub i zacząć wdrażać moduły usługi IoT Edge na urządzeniu. Użyj następujących poleceń na twoim urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zainstalowana i uruchomiona pomyślnie.  

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Utwórz listę uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)
