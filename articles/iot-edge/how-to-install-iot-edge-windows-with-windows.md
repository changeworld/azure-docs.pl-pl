---
title: Jak zainstalować usługi Azure IoT Edge na Windows za pomocą kontenerów Windows | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje dotyczące instalacji na Windows za pomocą kontenerów Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: 39e0de6b378ed61ab375c6468b58c8c4a87b5fb9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575968"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows do korzystania z kontenerów Windows

Środowisko uruchomieniowe usługi Azure IoT Edge jest wdrażana na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demona zabezpieczeń usługi IoT Edge** dostarcza i utrzymuje standardów zabezpieczeń na urządzeniu usługi Edge. Demon jest uruchamiany w każdym rozruchu i używa do ładowania urządzenia przez uruchomienie agenta usługi IoT Edge. **Agenta usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi Edge, w tym Centrum IoT Edge. **Centrum usługi IoT Edge** zarządza komunikacją między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na usługi Windows x64 (AMD/Intel) systemu. 

Obsługa Windows jest obecnie w wersji zapoznawczej.

## <a name="supported-windows-versions"></a>Obsługiwane wersje Windows
Usługa Azure IoT Edge przy użyciu kontenerów Windows mogą być używane z:
  * Windows 10 Enterprise na IoT/IoT Core z kwietnia 2018 r. Zaktualizuj (Tworzenie 17134).
  * System Windows Server w wersji 1803

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera 

>[!NOTE]
>W przypadku instalacji aparatu kontenera na Windows IoT Core, wykonaj kroki z [aprowizowanie urządzenia IoT Core artykułu] [ lnk-iot-core] , a następnie kontynuuj z poniższymi instrukcjami.

Usługa Azure IoT Edge opiera się na [zgodnego z OCI] [ lnk-oci] kontener środowiska uruchomieniowego (na przykład Docker). Możesz użyć [Docker for Windows] [ lnk-docker-for-windows] do tworzenia i testowania. 

Konfigurowanie platformy Docker for Windows [używać kontenerów Windows][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń usługi Azure IoT Edge

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń do aprowizowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

### <a name="install-and-manually-provision"></a>Instalacja oraz ręcznie zainicjować obsługę administracyjną

1. Postępuj zgodnie z instrukcjami w [zarejestrować nowe urządzenie usługi Azure IoT Edge] [ lnk-dcs] zarejestrować urządzenie i pobieranie parametrów połączenia urządzenia. 

2. Na urządzeniu usługi IoT Edge uruchom program PowerShell jako administrator. 

3. Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Po wyświetleniu monitu o **DeviceConnectionString**, wprowadzić parametry połączenia, z którego został pobrany z usługi IoT Hub. Nie należy dołączać cudzysłowu wokół parametry połączenia. 

### <a name="install-and-automatically-provision"></a>Instalowanie i automatycznie aprowizować

1. Postępuj zgodnie z instrukcjami w [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na Windows] [ lnk-dps] konfiguracji usługi Device Provisioning i pobrać jego **identyfikator zakresu**, symulowanie modułu TPM urządzenia i pobierania jej **identyfikator rejestracji**, następnie utworzyć rejestrację indywidualną. Gdy urządzenie jest zarejestrowane w usłudze IoT Hub, kontynuuj instalację.  

   >[!TIP]
   >Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania. 

2. Na urządzeniu usługi IoT Edge uruchom program PowerShell jako administrator. 

3. Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Po wyświetleniu monitu podaj **właściwości ScopeID** i **identyfikator** dla Twojej usługi aprowizacji i urządzenia. 

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Można sprawdzić stanu usługi IoT Edge przez: 

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi dla ostatnich 5 minut, przy użyciu:

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

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge][lnk-modules].

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z] [ lnk-trouble] strony.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md
[lnk-modules]: how-to-deploy-modules-portal.md
