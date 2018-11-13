---
title: Jak zainstalować usługi Azure IoT Edge na Windows za pomocą kontenerów systemu Linux | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje dotyczące instalacji na Windows za pomocą kontenerów systemu Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 7bae81f2f8b9ef9f7da45aed3bf8d7b6e15c9d0b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568271"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows do korzystania z kontenerów systemu Linux

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge z kontenerami systemu Linux na usługi Windows x64 (AMD/Intel) systemu. Obsługa Windows jest obecnie w wersji zapoznawczej.

>[!NOTE]
Używanie kontenerów systemu Linux na ows Windows nie jest produkcyjnych zalecane lub obsługiwanych konfiguracji dla usługi Azure IoT Edge. Jednak może służyć do tworzenia i testowania.

## <a name="supported-windows-versions"></a>Obsługiwane wersje Windows
Usługa Azure IoT Edge może być używane do tworzenia i testowania w następujących wersjach systemu Windows, korzystając z kontenerów systemu Linux:
  * System Windows 10 lub nowsze systemy operacyjne.
  * Windows Server 2016 lub nowych systemów operacyjnych serwera.

Aby uzyskać więcej informacji o tym, które są obecnie obsługiwane systemy operacyjne, zobacz [pomocy technicznej usługi Azure IoT Edge](support.md#operating-systems). 

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

Usługa Azure IoT Edge opiera się na [zgodnego z OCI](https://www.opencontainers.org/) kontener środowiska uruchomieniowego (na przykład Docker). 

Możesz użyć [Docker for Windows](https://www.docker.com/docker-windows) do tworzenia i testowania. Konfigurowanie platformy Docker for Windows [używania kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

### <a name="option-1-install-and-manually-provision"></a>Opcja 1: Instalacja i ręcznie aprowizacji

1. Postępuj zgodnie z instrukcjami w [zarejestrować nowe urządzenie usługi Azure IoT Edge](how-to-register-device-portal.md) zarejestrować urządzenie i pobieranie parametrów połączenia urządzenia. 

2. Na urządzeniu usługi IoT Edge uruchom program PowerShell jako administrator. 

3. Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Po wyświetleniu monitu o **DeviceConnectionString**, wprowadzić parametry połączenia, z którego został pobrany z usługi IoT Hub. Nie należy dołączać cudzysłowu wokół parametry połączenia. 

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalowanie i automatycznie przeprowadzać obsługę administracyjną

1. Postępuj zgodnie z instrukcjami w [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na Windows](how-to-auto-provision-simulated-device-windows.md) konfiguracji usługi Device Provisioning i pobrać jego **identyfikator zakresu**symulowane urządzenie TPM i pobrać jego  **Identyfikator rejestracji**, następnie utworzyć rejestrację indywidualną. Gdy urządzenie jest zarejestrowane w usłudze IoT Hub, kontynuuj instalację.  

   >[!TIP]
   >Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania. 

2. Na urządzeniu usługi IoT Edge uruchom program PowerShell jako administrator. 

3. Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. Po wyświetleniu monitu podaj **właściwości ScopeID** i **identyfikator** dla Twojej usługi aprowizacji i urządzenia.

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Można sprawdzić stanu usługi IoT Edge przez: 

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut, przy użyciu:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

I uruchomione moduły z listy:

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>Porady i sugestie

Jeśli sieci istnieje serwer proxy, wykonaj kroki opisane w [skonfigurować urządzenie usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md) próba zainstalowania i uruchomienia środowiska uruchomieniowego usługi IoT Edge.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.
