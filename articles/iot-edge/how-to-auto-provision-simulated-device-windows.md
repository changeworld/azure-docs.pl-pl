---
title: Automatyczne udostępnianie urządzeń z systemem Windows za pomocą dps — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Używanie symulowanego urządzenia na komputerze z systemem Windows do testowania automatycznego inicjowania obsługi administracyjnej urządzeń dla usługi Azure IoT Edge z usługą inicjowania obsługi urządzeń
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc051e2a0ebbdae7c62ff8a249747d118d3c2ce4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668688"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Tworzenie i udostępnianie symulowanego urządzenia Usługi IoT Edge za pomocą wirtualnego modułu TPM w systemie Windows

Urządzenia usługi Azure IoT Edge mogą być automatycznie aprowizacji przy użyciu [usługi inicjowania obsługi administracyjnej urządzeń,](../iot-dps/index.yml) podobnie jak urządzenia, które nie są włączone krawędzi. Jeśli nie znasz procesu automatycznego inicjowania obsługi administracyjnej, przejrzyj [pojęcia automatycznego inicjowania obsługi administracyjnej](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem.

Dps obsługuje symetryczne zaświadczanie klucza dla urządzeń IoT Edge zarówno w rejestracji indywidualnej, jak i rejestracji grupowej. W przypadku rejestracji grupowej, jeśli zaznaczysz opcję "czy urządzenie IoT Edge" jest true w poświadczeniu klucza symetrycznego, wszystkie urządzenia, które są zarejestrowane w tej grupie rejestracji zostaną oznaczone jako urządzenia IoT Edge.

W tym artykule pokazano, jak przetestować automatyczne inicjowanie obsługi administracyjnej na symulowanym urządzeniu Z krawędzią IoT w następujących krokach:

* Utwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzeń (DPS) centrum IoT.
* Utwórz symulowane urządzenie na komputerze z systemem Windows za pomocą symulowanego modułu TPM (Trusted Platform Module) dla zabezpieczeń sprzętu.
* Utwórz indywidualną rejestrację dla urządzenia.
* Zainstaluj środowisko uruchomieniowe usługi IoT Edge i podłącz urządzenie do centrum IoT Hub.

> [!TIP]
> W tym artykule opisano testowanie automatycznej inicjowania obsługi administracyjnej przy użyciu zaświadczania modułu TPM na urządzeniach wirtualnych, ale wiele z nich ma zastosowanie również podczas korzystania z fizycznego sprzętu modułu TPM.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer deweloperów systemu Windows. W tym artykule używa się systemu Windows 10.
* Aktywne Centrum IoT.

> [!NOTE]
> Moduł TPM 2.0 jest wymagany podczas korzystania z zaświadczania modułu TPM z dps i może być używany tylko do tworzenia rejestracji indywidualnych, a nie grupowych.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub

Utwórz nowe wystąpienie usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi Usługi IoT na platformie Azure i połącz je z centrum IoT hub. Postępuj zgodnie z instrukcjami w [konfigurowaniu dps centrum IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po uruchomieniu usługi inicjowania obsługi administracyjnej urządzeń skopiuj wartość **zakresu identyfikatora** ze strony przeglądowej. Ta wartość jest używana podczas konfigurowania środowiska wykonawczego usługi IoT Edge.

> [!TIP]
> Jeśli używasz fizycznego urządzenia TPM, musisz określić **klucz poręczenia,** który jest unikatowy dla każdego układu TPM i jest uzyskiwany od producenta układu TPM skojarzonego z nim. Unikatowy **identyfikator rejestracji** dla urządzenia TPM można uzyskać, na przykład tworząc skrót SHA-256 klucza poręczenia.
>
> Postępuj zgodnie z instrukcjami w artykule [Jak zarządzać rejestracjami urządzeń za pomocą usługi Azure Portal,](../iot-dps/how-to-manage-enrollments.md) aby utworzyć rejestrację w dps, a następnie kontynuować instalowanie środowiska [wykonawczego usługi IoT Edge](#install-the-iot-edge-runtime) w tym artykule, aby kontynuować.

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Utwórz symulowane urządzenie TPM na komputerze deweloperskim systemu Windows. Pobierz identyfikator **rejestracji** i **klucz poręczenia dla** urządzenia i użyj ich do utworzenia indywidualnego wpisu rejestracji w dps.

Podczas tworzenia rejestracji w dps, masz możliwość zadeklarowania **stanu bliźniaczej reprezentacji urządzenia początkowego**. W bliźniaczej reprezentacji urządzenia można ustawić znaczniki do grupowania urządzeń według dowolnej metryki, której potrzebujesz w rozwiązaniu, takiej jak region, środowisko, lokalizacja lub typ urządzenia. Tagi te są używane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

Wybierz język SDK, którego chcesz użyć do utworzenia symulowanego urządzenia, i wykonaj kroki do momentu utworzenia indywidualnej rejestracji.

Podczas tworzenia rejestracji indywidualnej wybierz opcję **Wartość True,** aby zadeklarować, że symulowane urządzenie TPM na komputerze deweloperskim systemu Windows jest **urządzeniem usługi IoT Edge**.

> [!TIP]
> W wierszu polecenia platformy Azure można utworzyć [rejestrację](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) lub [grupę rejestracji](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) i użyć flagi **obsługującej krawędź,** aby określić, że urządzenie lub grupa urządzeń jest urządzeniem usługi IoT Edge.

Symulowane przewodniki dotyczące urządzeń i poszczególnych rejestracji:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C #](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu rejestracji indywidualnej zapisz wartość identyfikatora **rejestracji**. Ta wartość jest używana podczas konfigurowania środowiska wykonawczego usługi IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska wykonawczego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki są uruchamiane w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchomić kod na krawędzi.

Podczas inicjowania obsługi administracyjnej urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Identyfikator **rejestracji** urządzenia, który został utworzony

Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu z uruchomionym symulowanym modułem TPM. Środowiska wykonawczego IoT Edge można skonfigurować do automatycznego, a nie ręcznego inicjowania obsługi administracyjnej.

> [!TIP]
> Zachowaj otwarte okno z uruchomieniem symulatora modułu TPM podczas instalacji i testowania.

Aby uzyskać bardziej szczegółowe informacje dotyczące instalowania usługi IoT Edge w systemie Windows, w tym wymagania wstępne i instrukcje dotyczące zadań, takich jak zarządzanie kontenerami i aktualizowanie usługi IoT Edge, zobacz [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby podczas instalowania aplikacji IoT Edge, a nie programu PowerShell (x86), należy używać sesji programu AMD64 programu PowerShell.

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby i środowisko wykonawcze usługi IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie ponownie uruchomione. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, uruchom program PowerShell jako administrator ponownie.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi administracyjnej za pomocą kontenerów systemu Windows. Użyj `-Dps` flagi, aby użyć usługi inicjowania obsługi administracyjnej urządzeń zamiast ręcznego inicjowania obsługi administracyjnej.

   Zastąp wartości `{scope_id}` `{registration_id}` zastępcze dla i z danymi zebranymi wcześniej.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Jeśli środowisko wykonawcze rozpoczęło się pomyślnie, możesz przejść do centrum IoT Hub i rozpocząć wdrażanie modułów usługi IoT Edge na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko wykonawcze zostało zainstalowane i uruchomione pomyślnie.  

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi inicjowania obsługi administracyjnej urządzeń umożliwia ustawienie identyfikatora urządzenia i bliźniaczych tagów urządzenia w tym samym czasie, co aprowizowanie nowego urządzenia. Za pomocą tych wartości można kierować reklamy na poszczególne urządzenia lub grupy urządzeń za pomocą automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)
