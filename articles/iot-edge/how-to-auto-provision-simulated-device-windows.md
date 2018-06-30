---
title: Urządzenie brzegowe IoT Azure automatycznie udostępnić z punktu dystrybucji — Windows | Dokumentacja firmy Microsoft
description: Użyj symulowanego urządzenia na komputerze z systemem Windows do testowania, automatyczne Inicjowanie obsługi administracyjnej urządzeń Edge IoT Azure z usługą inicjowania obsługi urządzeń
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a973b248022cf3c0497f72bc2fcdd45a6527e65
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116123"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Tworzenie i przydzielanie symulowane urządzenie brzegowe modułu TPM w systemie Windows

Urządzenia brzegowe IoT Azure mogą być automatycznie elastycznie przy użyciu [usługi inicjowania obsługi urządzeń](../iot-dps/index.yml) podobnie jak urządzeń, których nie włączono krawędzi. Jeśli znasz proces automatycznego inicjowania obsługi administracyjnej, przejrzyj [automatycznego inicjowania obsługi administracyjnej pojęcia](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem. 

W tym artykule przedstawiono sposób testu automatycznego inicjowania obsługi administracyjnej na symulowane urządzenie brzegowe następujące czynności: 

* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (punktu dystrybucji).
* Na komputerze z systemem Windows z symulowanego Trusted Platform Module modułu () zabezpieczeń sprzętu, należy utworzyć symulowane urządzenie.
* Tworzenie pojedynczych rejestracji dla urządzenia.
* Zainstaluj środowisko uruchomieniowe krawędzi IoT i podłącz urządzenie z Centrum IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna rozwoju systemu Windows. W tym artykule używa systemu Windows 10. 
* Aktywnym Centrum IoT. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Skonfiguruj usługę inicjowania obsługi urządzeń Centrum IoT

Utwórz nowe wystąpienie klasy usługa inicjowania obsługi urządzeń Centrum IoT na platformie Azure i połącz go z Centrum IoT. Można postępuj zgodnie z instrukcjami [konfigurowania punktu dystrybucji z Centrum IoT](../iot-dps/quick-setup-auto-provision.md).

Po umieszczeniu działanie usługi inicjowania obsługi urządzeń, skopiować wartość **identyfikator zakresu** na stronie Przegląd. Ta wartość jest użyta podczas konfigurowania środowiska uruchomieniowego IoT krawędzi. 

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Utworzyć symulowane urządzenie modułu TPM na komputerze deweloperskim systemu Windows. Pobrać **identyfikator rejestracji** i **klucz poręczenia** dla danego urządzenia i umożliwia utworzenie wpisu rejestracji poszczególnych w punktu dystrybucji. 

Po utworzeniu rejestrowania w punktu dystrybucji, masz możliwość zadeklarować **stan dwie urządzenia początkowego**. W dwie urządzenia można ustawić tagi do grupy urządzeń przez dowolnego metrykę, które należy w rozwiązaniu, takie jak typ regionu, środowisko, lokalizacji lub urządzenia. Tagi te są wykorzystywane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md). 

Wybierz język zestawu SDK, który chcesz użyć, aby utworzyć symulowane urządzenie i wykonaj kroki, dopóki nie zostaną utworzone poszczególnych rejestracji. 

Podczas tworzenia poszczególnych rejestracji wybrać **włączyć** Aby zadeklarować, że ta maszyna wirtualna jest **urządzenia IoT**.

Symulowane urządzenie i przewodniki dotyczące poszczególnych rejestracji: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu poszczególnych rejestracji, Zapisz wartość **identyfikator rejestracji**. Ta wartość jest użyta podczas konfigurowania środowiska uruchomieniowego IoT krawędzi. 

## <a name="install-the-iot-edge-runtime"></a>Zainstaluj środowisko uruchomieniowe krawędzi IoT

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki Uruchom w kontenerach i pozwala na wdrażanie kontenery dodatkowe do urządzenia, co umożliwia uruchamianie kodu na krawędzi. Na urządzeniach z systemem Windows można użyć kontenery systemu Windows lub Linux kontenerów. Wybierz typ kontenerów, które ma być używany i postępuj zgodnie z instrukcjami. Upewnij się skonfigurować środowisko uruchomieniowe krawędzi IoT do automatycznego, nie ręcznego inicjowania obsługi. 

Postępuj zgodnie z instrukcjami, aby zainstalować środowisko uruchomieniowe IoT Edge na urządzeniu jest uruchomiona symulowane modułu TPM z poprzedniej sekcji. 

Wiedzieć z punktu dystrybucji **identyfikator zakresu** i urządzenia **identyfikator rejestracji** przed rozpoczęciem tych artykułach. 

* [Kontenery systemu Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Kontenery systemu Linux](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>Utwórz zmienną środowiskową modułu TPM

Na komputerze, na którym działa symulowane urządzenie, zmodyfikuj **iotedge** usługi rejestru, aby ustawić wartość zmiennej środowiskowej.

1. Z **Start** menu, otwórz **regedit**. 
2. Przejdź do **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Wybierz **Edytuj** > **nowe** > **wartość ciągu wielokrotnego**. 
4. Wprowadź nazwę **środowiska**. 
5. Kliknij dwukrotnie nową zmienną i ustaw wartości dane **IOTEDGE_USE_TPM_DEVICE = ON**. 
6. Kliknij przycisk **OK**, aby zapisać zmiany. 

## <a name="restart-the-iot-edge-runtime"></a>Uruchom ponownie środowisko uruchomieniowe krawędzi IoT

Tak, aby go przejmuje wszystkie zmiany konfiguracji wprowadzone na urządzeniu, należy ponownie uruchomić środowisko uruchomieniowe IoT krawędzi. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

Sprawdź, czy działa środowisko uruchomieniowe IoT krawędzi. 

   ```bash
   sudo systemctl status iotedge
   ```

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko uruchomieniowe został uruchomiony pomyślnie, można przejść do Centrum IoT i czy nowe urządzenie automatycznie zainicjowano obsługę administracyjną i jest gotowy do uruchomienia krawędzi IoT modułów. 

Sprawdź stan usługi IoT krawędzi.

```powershell
Get-Service iotedge
```

Przeanalizuj dzienniki usługi z ostatnich 5 minut.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Utwórz listę uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Proces rejestracji urządzeń usługi udostępniania pozwala ustawić identyfikator urządzenia i tagi dwie urządzenia w tym samym czasie, jak udostępnić nowe urządzenie. Te wartości można użyć pod kątem poszczególnych urządzeń lub grup urządzeń przy użyciu narzędzia do zarządzania urządzeniami automatycznego. Dowiedz się, jak [monitor krawędzi IoT modułów na skalowanie przy użyciu portalu Azure i wdróż](how-to-deploy-monitor.md) lub [przy użyciu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)