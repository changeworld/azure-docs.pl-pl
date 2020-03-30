---
title: Instalowanie usługi Azure IoT Edge dla systemu Linux w systemie Windows | Dokumenty firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge dla kontenerów systemu Linux w systemach Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133157"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Uruchamianie kontenerów systemu Linux za pomocą usługi IoT Edge w systemie Windows

Testowanie modułów IoT Edge dla urządzeń z systemem Linux przy użyciu komputera z systemem Windows.

W scenariuszu produkcyjnym urządzenia z systemem Windows powinny uruchamiać tylko kontenery systemu Windows. Jednak typowy scenariusz rozwoju jest użycie komputera z systemem Windows do tworzenia modułów usługi IoT Edge dla urządzeń z systemem Linux. Środowisko wykonawcze usługi IoT Edge dla systemu Windows umożliwia uruchamianie kontenerów systemu Linux w celach **testowych i** programistycznych.

W tym artykule wymieniono kroki instalowania środowiska wykonawczego usługi Azure IoT Edge przy użyciu kontenerów systemu Linux w systemie Windows x64 (AMD/Intel). Aby dowiedzieć się więcej o instalatorze środowiska wykonawczego usługi IoT Edge, w tym o szczegółach dotyczących wszystkich parametrów instalacji, zobacz [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

Aby uzyskać informacje o tym, co znajduje się w najnowszej wersji usługi IoT Edge, zobacz [wersje usługi Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja służy do sprawdzenia, czy urządzenie z systemem Windows może obsługiwać usługi IoT Edge i przygotować je do aparatu kontenera przed instalacją.

### <a name="supported-windows-versions"></a>Obsługiwane wersje systemu Windows

Usługa Azure IoT Edge z kontenerami systemu Linux może działać w dowolnej wersji systemu Windows, która spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Jeśli chcesz zainstalować usługę IoT Edge na maszynie wirtualnej, włącz zagnieżdżoną wirtulizację i przydziel co najmniej 2 GB pamięci. Sposób włączania wirtualizacji zagnieżdżonej różni się w zależności od hipernadzorcy używanego. W przypadku funkcji Hyper-V maszyny wirtualne generacji 2 mają domyślnie włączoną wirtualizację zagnieżdżoną. W przypadku oprogramowania VMWare istnieje przełącznik umożliwiający włączenie tej funkcji na maszynie wirtualnej.

### <a name="prepare-the-container-engine"></a>Przygotowanie silnika kontenerowego

Usługa Azure IoT Edge opiera się na silniku kontenera [zgodnego z OCI.](https://www.opencontainers.org/) Największą różnicą konfiguracji między uruchamianiem kontenerów systemu Windows i Linux na komputerze z systemem Windows jest to, że instalacja usługi IoT Edge zawiera środowisko wykonawcze kontenera systemu Windows, ale przed zainstalowaniem usługi IoT Edge należy udostępnić własny czas wykonywania kontenerów systemu Linux.

Aby skonfigurować komputer z systemem Windows do tworzenia i testowania kontenerów dla urządzeń z systemem Linux, można użyć [pulpitu platformy Docker](https://www.docker.com/docker-windows) jako aparatu kontenera. Przed zainstalowaniem usługi IoT Edge należy zainstalować program Docker i skonfigurować go do [używania kontenerów systemu Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)  

Jeśli urządzenie Usługi IoT Edge jest komputerem z systemem Windows, sprawdź, czy spełnia [wymagania systemowe funkcji](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalowanie usługi IoT Edge na nowym urządzeniu

>[!NOTE]
>Pakiety oprogramowania usługi Azure IoT Edge podlegają postanowieniu licencyjnym znajdującym się w pakietach (w katalogu LICENSE). Prosimy o zapoznanie się z postanowieniami licencyjne przed użyciem pakietu. Instalacja i korzystanie z pakietu oznacza akceptację niniejszych warunków. Jeśli nie zgadzasz się z postanowieniami licencyjnymi, nie korzystaj z pakietu.

Skrypt programu PowerShell pobiera i instaluje demona zabezpieczeń usługi Azure IoT Edge. Demon zabezpieczeń następnie uruchamia pierwszy z dwóch modułów środowiska uruchomieniowego, agenta usługi IoT Edge, który umożliwia zdalne wdrażanie innych modułów.

Po zainstalowaniu środowiska wykonawczego usługi IoT Edge po raz pierwszy na urządzeniu należy aprowizować urządzenie z tożsamością z centrum IoT hub. Pojedyncze urządzenie usługi IoT Edge można aprowizować ręcznie przy użyciu ciągu połączeń urządzenia dostarczonego przez koncentrator IoT hub. Możesz też użyć usługi inicjowania obsługi administracyjnej urządzeń, aby automatycznie aprowizować urządzenia, co jest przydatne, gdy masz wiele urządzeń do skonfigurowania.

Więcej informacji na temat różnych opcji i parametrów instalacji można przeczytać w artykule [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md). Po zainstalowaniu i skonfigurowaniu programu Docker Desktop dla kontenerów systemu Linux główną różnicą w instalacji jest deklarowanie systemu Linux za pomocą parametru **-ContainerOs.** Przykład:

1. Jeśli jeszcze tego nie zrobiłeś, zarejestruj nowe urządzenie usługi IoT Edge i pobierz parametry połączenia urządzenia. Skopiuj parametry połączenia, aby użyć go w dalszej części tej sekcji. Ten krok można wykonać za pomocą następujących narzędzi:

   * [Portal Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Kod programu Visual Studio](how-to-register-device.md#register-with-visual-studio-code)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji programu PowerShell dla systemu AMD64, aby zainstalować usługę IoT Edge, a nie program PowerShell (x86). Jeśli nie masz pewności, którego typu sesji używasz, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby (które nie jest używane dla kontenerów systemu Linux) i środowisko wykonawcze usługi IoT Edge. Polecenie domyślnie kontenery systemu Windows, więc zadeklarować Linux jako żądany system operacyjny kontenera.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. W tym momencie urządzenia IoT Core mogą zostać automatycznie ponownie uruchomione. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, uruchom program PowerShell jako administrator ponownie.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi administracyjnej za pomocą ciągu połączenia urządzenia. Ponownie zadeklaruj Linuksa jako żądany system operacyjny kontenera.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w kroku 1. Ciąg połączenia urządzenia kojarzy urządzenie fizyczne z identyfikatorem urządzenia w centrum IoT Hub.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać cudzysłowów:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Sprawdź stan usługi IoT Edge:

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Uruchom automatyczne sprawdzanie najczęściej występujących błędów konfiguracji i sieci:

```powershell
iotedge check
```

Lista uruchomionych modułów. Po nowej instalacji, jedynym modułem, który powinien zobaczyć uruchomiony jest **edgeAgent**. Po [pierwszym wdrożeniu modułów usługi IoT Edge](how-to-deploy-modules-portal.md) na urządzeniu zostanie uruchomiony również inny moduł systemowy **edgeHub.**

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie IoT Edge aprowizowane z zainstalowanym czasem wykonywania, można [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z prawidłową instalacją aplikacji IoT Edge, zapoznaj się ze stroną [rozwiązywania problemów.](troubleshoot.md)

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska wykonawczego](how-to-update-iot-edge.md).
