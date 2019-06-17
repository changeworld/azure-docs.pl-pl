---
title: Instalowanie usługi Azure IoT Edge dla systemu Linux na Windows | Dokumentacja firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge dla kontenerów systemu Linux w systemie Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151703"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Korzystanie z usługi IoT Edge na Windows umożliwiające uruchamianie kontenerów systemu Linux

Przetestuj moduły usługi IoT Edge dla urządzeń z systemem Linux za pomocą maszyny z systemem Windows. 

W scenariuszu produkcji urządzenia Windows tylko uruchamiać kontenery Windows. Jednak typowy scenariusz tworzenia jest używanie komputera Windows do tworzenia usługi IoT Edge modułów dla urządzeń z systemem Linux. Środowisko uruchomieniowe usługi IoT Edge, for Windows umożliwia uruchamianie kontenerów systemu Linux dla **środowisk testowych i programistycznych** celów. 

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge, przy użyciu kontenerów systemu Linux w swojej Windows x64 (AMD/Intel) systemu. Aby dowiedzieć się więcej o Instalatorze środowisko uruchomieniowe usługi IoT Edge zawierającej szczegóły dotyczące wszystkich parametrów instalacji, zobacz [zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

Użyj tej sekcji, aby sprawdzić, czy urządzenie Windows może obsługiwać usługi IoT Edge i przygotować go do aparatu kontenera, przed rozpoczęciem instalacji. 

### <a name="supported-windows-versions"></a>Obsługiwane wersje Windows

Usługa Azure IoT Edge z kontenerami systemu Linux można uruchomić w następujących wersjach systemu Windows: 
* Windows 10 rozliczenia aktualizacji (kompilacja 14393) lub nowszej
* Windows Server 2016 lub nowszy

Aby uzyskać więcej informacji o to, co jest uwzględnione w najnowszej wersji usługi IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Jeśli chcesz zainstalować usługi IoT Edge na maszynie wirtualnej, włączać wirtualizację zagnieżdżoną i przydziel co najmniej 2 GB pamięci RAM. Jak włączać wirtualizację zagnieżdżoną różni się w zależności od funkcji hypervisor użytkowanie. Dla funkcji Hyper-V maszyny wirtualne generacji 2 mają zagnieżdżone wirtualizacji włączona domyślnie. Dla oprogramowania VMWare Brak przełącznika, aby włączyć tę funkcję na maszynie wirtualnej. 

### <a name="prepare-the-container-engine"></a>Przygotowanie aparatu kontenera 

Usługa Azure IoT Edge opiera się na [zgodnego z OCI](https://www.opencontainers.org/) kontenera aparatu. Największa różnica konfiguracji między systemem Windows i Linux kontenery Windows, maszyna jest, że instalacja usługi IoT Edge obejmuje kontener środowiska uruchomieniowego Windows, ale musisz podać własne środowisko uruchomieniowe dla kontenerów systemu Linux, przed zainstalowaniem usługi IoT Edge. 

Aby skonfigurować maszynę z systemem Windows do tworzenia i testowania kontenery dla urządzeń z systemem Linux, można użyć [pulpitu platformy Docker](https://www.docker.com/docker-windows) jako aparat kontenera. Należy zainstalować platformę Docker i skonfigurować go do [korzystanie z kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) przed zainstalowaniem usługi IoT Edge.  

Jeśli urządzenia usługi IoT Edge jest komputerem Windows, sprawdź, czy spełnia [wymagania systemowe](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) funkcji Hyper-v.

## <a name="install-iot-edge-on-a-new-device"></a>Zainstaluj usługi IoT Edge na nowe urządzenie

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Skrypt programu PowerShell pobiera i instaluje demona zabezpieczeń usługi Azure IoT Edge. Demon zabezpieczeń następnie uruchamia pierwsze dwa moduły środowiska uruchomieniowego, agent usługi IoT Edge, który umożliwia używanie wdrożeń zdalnego innych modułów. 

Po zainstalowaniu środowiska uruchomieniowego usługi IoT Edge po raz pierwszy na urządzeniu, musisz aprowizować urządzenia przy użyciu tożsamości z usługi IoT hub. Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez Centrum IoT hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń, aby skonfigurować. 

Możesz dowiedzieć się więcej o opcjach innej instalacji i parametrów w artykule [zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na Windows](how-to-install-iot-edge-windows.md). Po utworzeniu pulpitu platformy Docker zainstalowany i skonfigurowany dla kontenerów systemu Linux, różnica głównej instalacji jest zadeklarowanie systemu Linux przy użyciu **- ContainerOs** parametru. Na przykład: 

1. Jeśli jeszcze nie, zarejestrować nowe urządzenie usługi IoT Edge i pobieranie parametrów połączenia urządzenia. Skopiuj parametry połączenia do użycia w przyszłości w tej sekcji. Możesz wykonać ten krok za pomocą następujących narzędzi:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Aby zainstalować usługi IoT Edge, PowerShell (x86), należy użyć AMD64 sesji programu PowerShell. Jeśli nie masz pewności, jakiego typu sesji używasz, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **IoTEdge Wdróż** polecenie sprawdza, czy komputer Windows jest w obsługiwanej wersji, włączenie funkcji kontenerów i pliki do pobrania moby środowiska uruchomieniowego, (która nie jest używany dla kontenerów systemu Linux) i środowisko uruchomieniowe usługi IoT Edge. Ustawienia domyślne polecenia, aby kontenery Windows, więc zadeklarować systemu Linux jako system operacyjny żądanego kontenera. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. W tym momencie urządzenia IoT Core może automatycznie uruchomiony ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server może monitować użytkownika o ponowne uruchomienie. Jeśli tak, teraz ponownie uruchomić urządzenie. Gdy urządzenie jest gotowe, ponownie uruchom program PowerShell jako administrator.

5. **IoTEdge zainicjować** polecenie konfiguruje środowisko uruchomieniowe usługi IoT Edge na urządzeniu. Polecenie domyślne z ręcznego inicjowania obsługi administracyjnej za pomocą parametrów połączenia urządzenia. Ponownie zadeklarować systemu Linux jako system operacyjny żądanego kontenera. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia, do którego został pobrany w kroku 1. Parametry połączenia urządzenia kojarzy urządzenia fizycznego identyfikator urządzenia w usłudze IoT Hub. 

   Parametry połączenia urządzenia ma następujący format i nie może zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Sprawdź stan usługi IoT Edge. Powinny być wymienione jako uruchomiony.  

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Utwórz listę uruchomionych modułów. Po zainstalowaniu nowego tylko moduł powinien zostać wyświetlony jest uruchomiona **edgeAgent**. Po zakończeniu [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md), widoczne będą inne. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy z instalacją usługi IoT Edge prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).
