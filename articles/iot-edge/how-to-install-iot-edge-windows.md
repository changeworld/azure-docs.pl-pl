---
title: Instalowanie Azure IoT Edge w systemie Windows | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge w systemach Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 7f20e04fa65d0266d9e77b8bbcf2e2c4b1fd9eab
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227456"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Windows

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat środowiska uruchomieniowego IoT Edge, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge w systemie Windows x64 (AMD/Intel) przy użyciu kontenerów systemu Windows.

> [!NOTE]
> Znany problem z systemem operacyjnym Windows zapobiega przejściu do trybu uśpienia i hibernacji w przypadku, gdy są uruchomione moduły IoT Edge (kontenery systemu Windows nano Server w systemie). Ten problem wpływa na czas pracy baterii na urządzeniu.
>
> W celu obejścia tego problemu należy `Stop-Service iotedge` użyć polecenia, aby zatrzymać wszystkie uruchomione moduły IoT Edge przed użyciem tych stanów. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Korzystanie z kontenerów systemu Linux w systemach Windows nie jest zalecaną lub obsługiwaną konfiguracją produkcyjną dla Azure IoT Edge. Jednak może służyć do tworzenia i testowania. Aby dowiedzieć się więcej, zobacz [używanie IoT Edge w systemie Windows do uruchamiania kontenerów systemu Linux](how-to-install-iot-edge-windows-with-linux.md).

Aby uzyskać informacje o tym, co zawiera Najnowsza wersja IoT Edge, zobacz [wersje Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja służy do sprawdzenia, czy urządzenie z systemem Windows może obsługiwać IoT Edge i przygotować je do aparatu kontenera przed instalacją. 

### <a name="supported-windows-versions"></a>Obsługiwane wersje Windows

W przypadku scenariuszy projektowania i testowania Azure IoT Edge z kontenerami systemu Windows można zainstalować w dowolnej wersji systemu Windows 10 lub Windows Server 2019 (kompilacja 17763), która obsługuje funkcję kontenerów. Informacje o tym, które systemy operacyjne są obecnie obsługiwane w scenariuszach produkcyjnych, można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Przygotowanie do aparatu kontenera 

Azure IoT Edge opiera się na aparacie kontenera zgodnym ze [sterownikiem OCI](https://www.opencontainers.org/) . W przypadku scenariuszy produkcyjnych Użyj aparatu Moby zawartego w skrypcie instalacji, aby uruchomić kontenery systemu Windows na urządzeniu z systemem Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Instalowanie IoT Edge na nowym urządzeniu

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Skrypt programu PowerShell pobiera i instaluje demona Security Azure IoT Edge. Następnie demon zabezpieczeń uruchamia pierwsze dwa moduły środowiska uruchomieniowego, Agent IoT Edge, który umożliwia zdalne wdrażanie innych modułów. 

W przypadku instalowania środowiska uruchomieniowego IoT Edge po raz pierwszy na urządzeniu należy zainicjować obsługę administracyjną urządzenia z tożsamością usługi IoT Hub. Pojedyncze urządzenie IoT Edge może być inicjowane ręcznie przy użyciu parametrów połączenia urządzenia dostarczonych przez IoT Hub. Można też użyć usługi Device Provisioning (DPS), aby automatycznie zainicjować obsługę administracyjną urządzeń, co jest przydatne, gdy istnieje wiele urządzeń do skonfigurowania. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

W poniższych sekcjach opisano typowe przypadki użycia i parametry skryptu instalacji IoT Edge na nowym urządzeniu. 

### <a name="option-1-install-and-manually-provision"></a>Option 1: Instalacja oraz ręcznie zainicjować obsługę administracyjną

W tej pierwszej opcji podano **Parametry połączenia urządzenia** wygenerowane przez IoT Hub w celu udostępnienia urządzenia. 

W tym przykładzie przedstawiono instalację ręczną przy użyciu kontenerów systemu Windows:

1. Jeśli jeszcze tego nie zrobiono, Zarejestruj nowe urządzenie IoT Edge i Pobierz **Parametry połączenia urządzenia**. Skopiuj parametry połączenia, aby użyć ich w dalszej części tej sekcji. Ten krok można wykonać przy użyciu następujących narzędzi:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji AMD64 programu PowerShell, aby zainstalować IoT Edge, a nie PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w kroku 1. Parametry połączenia urządzenia kojarzą urządzenie fizyczne z IDENTYFIKATORem urządzenia w IoT Hub. 

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać znaków cudzysłowu:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Wykonaj kroki opisane w sekcji Weryfikowanie pomyślnej [instalacji](#verify-successful-installation) , aby sprawdzić stan IoT Edge na urządzeniu. 

W przypadku ręcznego instalowania i aprowizacji urządzenia można użyć dodatkowych parametrów do zmodyfikowania instalacji, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatora w trybie offline
* Zadeklaruj określony obraz kontenera agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź z wyprzedzeniem, aby dowiedzieć się więcej na temat [wszystkich parametrów instalacji](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalowanie i automatycznie aprowizować

W tej drugiej opcji można zainicjować obsługę administracyjną urządzenia przy użyciu IoT Hub Device Provisioning Service. Podaj **Identyfikator zakresu** z wystąpienia usługi Device Provisioning i **Identyfikator rejestracji** z Twojego urządzenia. Dodatkowe wartości mogą być wymagane zgodnie z mechanizmem zaświadczania podczas aprowizacji za pomocą usługi DPS, na przykład przy użyciu [kluczy symetrycznych](how-to-auto-provision-symmetric-keys.md).

Poniższy przykład ilustruje instalację automatyczną przy użyciu kontenerów systemu Windows i zaświadczania modułu TPM:

1. Wykonaj kroki opisane w temacie [Tworzenie i Inicjowanie obsługi symulowanego modułu TPM IoT Edge w systemie Windows](how-to-auto-provision-simulated-device-windows.md) w celu skonfigurowania usługi Device Provisioning i pobrania jej **identyfikatora zakresu**, symulowania urządzenia TPM i pobrania **identyfikatora rejestracji**, a następnie utworzenia indywidualnego rejestracyjn. Po zarejestrowaniu urządzenia w usłudze IoT Hub wykonaj te kroki instalacji.  

   >[!TIP]
   >Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania. 

1. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji AMD64 programu PowerShell, aby zainstalować IoT Edge, a nie PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows. `-Dps` Użyj flagi, aby użyć usługi Device Provisioning zamiast ręcznego inicjowania obsługi.

   Użycie polecenia **Initialize-IoTEdge** w celu użycia usługi DPS z zaświadczeniem modułu TPM:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

   Użycie polecenia **Initialize-IoTEdge** w celu użycia usługi DPS z zaświadczeniem klucza symetrycznego. Zamień `{symmetric key}` na klucz urządzenia.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -SymmetricKey {symmetric key}
   ```

1. Po wyświetleniu monitu podaj identyfikator zakresu z usługi Device Provisioning i Identyfikator rejestracji z urządzenia, z którego wszystkie mają zostać pobrane w kroku 1.

1. Wykonaj kroki opisane w sekcji Weryfikowanie pomyślnej [instalacji](#verify-successful-installation) , aby sprawdzić stan IoT Edge na urządzeniu. 

W przypadku ręcznego instalowania i aprowizacji urządzenia można użyć dodatkowych parametrów do zmodyfikowania instalacji, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatora w trybie offline
* Zadeklaruj określony obraz kontenera agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź do tego artykułu lub przejdź do informacji o [wszystkich parametrach instalacji](#all-installation-parameters).

## <a name="offline-installation"></a>Instalacja w trybie offline

Podczas instalacji pobierane są dwa pliki:

* Microsoft Azure IoT Edge CAB, który zawiera demo IoT Edge Security daemon (iotedged), aparat kontenera Moby i interfejs wiersza polecenia Moby.
* Visual C++ redystrybucyjny pakiet (VC Runtime) MSI

Możesz pobrać jeden lub oba te pliki z wyprzedzeniem do urządzenia, a następnie wskazać skrypt instalacji w katalogu zawierającym pliki. Instalator najpierw sprawdza katalog, a następnie pobiera tylko składniki, które nie zostały odnalezione. Jeśli wszystkie pliki są dostępne w trybie offline, możesz zainstalować program bez połączenia z Internetem. Za pomocą tej funkcji można także zainstalować określoną wersję składników programu.  

Najnowsze pliki instalacyjne IoT Edge wraz z poprzednimi wersjami znajdują się w artykule [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

Aby zainstalować składniki w trybie offline, użyj `-OfflineInstallationPath` parametru w ramach polecenia Deploy-IoTEdge i podaj ścieżkę bezwzględną do katalogu plików. Na przykład

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Można również użyć parametru ścieżka instalacji w trybie offline za pomocą polecenia Update-IoTEdge wprowadzonego w dalszej części tego artykułu. 

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Sprawdź stan usługi IoT Edge. Powinien być wymieniony jako uruchomiony.  

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut. Po zakończeniu instalowania środowiska uruchomieniowego IoT Edge może zostać wyświetlona lista błędów od czasu między uruchomieniem metody **Deploy-IoTEdge** i **Initialize-IoTEdge**. Te błędy są oczekiwane, ponieważ próba uruchomienia usługi przed jej skonfigurowaniem. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Utwórz listę uruchomionych modułów. Po nowej instalacji jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**. Po [wdrożeniu modułów IoT Edge](how-to-deploy-modules-portal.md)zobaczysz inne. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Zarządzanie kontenerami modułów

Usługa IoT Edge wymaga aparatu kontenera działającego na urządzeniu. Po wdrożeniu modułu na urządzeniu środowisko uruchomieniowe IoT Edge używa aparatu kontenera do ściągania obrazu kontenera z rejestru w chmurze. Usługa IoT Edge umożliwia korzystanie z modułów i pobieranie dzienników, ale czasami warto używać aparatu kontenera do współpracy z kontenerem. 

Aby uzyskać więcej informacji o pojęciach dotyczących modułu, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md). 

Jeśli używasz kontenerów systemu Windows na urządzeniu z systemem Windows IoT Edge, wówczas instalacja IoT Edge obejmowała aparat kontenera Moby. Aparat Moby opierał się na tych samych standardach jak Docker i został zaprojektowany do równoległego uruchamiania na tym samym komputerze co program Docker Desktop. Z tego powodu, jeśli chcesz, aby obiekty docelowe były zarządzane przez aparat Moby, konieczne jest przeanalizowanie tego aparatu zamiast platformy Docker. 

Na przykład aby wyświetlić listę wszystkich obrazów platformy Docker, użyj następującego polecenia:

```powershell
docker images
```

Aby wyświetlić listę wszystkich obrazów Moby, należy zmodyfikować to samo polecenie ze wskaźnikiem do aparatu Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identyfikator URI aparatu jest wyświetlany w danych wyjściowych skryptu instalacji lub można go znaleźć w sekcji Ustawienia środowiska uruchomieniowego kontenera dla pliku config. YAML. 

![Identyfikator URI moby_runtime w pliku config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Aby uzyskać więcej informacji o poleceniach, których można użyć do współdziałania z kontenerami i obrazami działającymi na urządzeniu, zobacz [interfejs wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aktualizowanie istniejącej instalacji

Jeśli środowisko uruchomieniowe IoT Edge zostało już zainstalowane na urządzeniu przed i udostępnione przy użyciu tożsamości z IoT Hub, można zaktualizować środowisko uruchomieniowe bez konieczności ponownego wprowadzania informacji o urządzeniu. 

Aby uzyskać więcej informacji, zobacz temat [aktualizowanie IoT Edge zabezpieczeń i środowiska uruchomieniowego](how-to-update-iot-edge.md).

Ten przykład pokazuje instalację wskazującą istniejący plik konfiguracji i używa kontenerów systemu Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Podczas aktualizacji IoT Edge można użyć dodatkowych parametrów do zmodyfikowania aktualizacji, w tym:

* Ruch bezpośredni do przechodzenia przez serwer proxy lub
* Wskaż Instalatora w trybie offline 
* Ponowne uruchamianie bez monitu w razie potrzeby

Nie można zadeklarować obrazu kontenera IoT Edge agenta za pomocą parametrów skryptu, ponieważ te informacje zostały już ustawione w pliku konfiguracji z poprzedniej instalacji. Jeśli chcesz zmodyfikować obraz kontenera agenta, zrób to w pliku config. YAML. 

Aby uzyskać więcej informacji na temat tych opcji aktualizacji, użyj `Get-Help Update-IoTEdge -full` polecenia lub zapoznaj się ze [wszystkimi parametrami instalacji](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Windows, użyj następującego polecenia z okna administracyjnego programu PowerShell. To polecenie usuwa środowisko uruchomieniowe IoT Edge wraz z istniejącą konfiguracją i danymi aparatu Moby. 

```powershell
Uninstall-IoTEdge
```

Polecenie Uninstall-IoTEdge nie działa w przypadku systemu Windows IoT Core. Aby usunąć IoT Edge z urządzeń z systemem Windows IoT Core, należy ponownie wdrożyć obraz systemu Windows IoT Core. 

Aby uzyskać więcej informacji o opcjach odinstalowywania, użyj polecenia `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Wszystkie parametry instalacji

W poprzednich sekcjach wprowadzono typowe scenariusze instalacji z przykładami użycia parametrów w celu zmodyfikowania skryptu instalacji. Ta sekcja zawiera tabele referencyjne typowych parametrów używanych do instalowania, aktualizowania lub odinstalowywania IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Polecenie Deploy-IoTEdge pobiera i wdraża demona zabezpieczeń IoT Edge i jej zależności. Polecenie wdrożenia akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Deploy-IoTEdge -full`.  

| Parametr | Akceptowane wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Dawaj | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Polecenie Initialize-IoTEdge konfiguruje IoT Edge przy użyciu parametrów połączenia urządzenia i szczegółów operacyjnych. Większość informacji generowanych przez to polecenie jest następnie przechowywana w pliku iotedge\config.YAML. Polecenie inicjujące akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Initialize-IoTEdge -full`. 

| Parametr | Akceptowane wartości | Komentarze |
| --------- | --------------- | -------- |
| **Ręcznie** | Brak | **Parametr przełącznika**. Jeśli typ aprowizacji nie zostanie określony, wartość domyślna to ręczna.<br><br>Deklaruje, że podajesz parametry połączenia urządzenia, aby ręcznie zainicjować obsługę urządzenia |
| **Dokumenty** | Brak | **Parametr przełącznika**. Jeśli typ aprowizacji nie zostanie określony, wartość domyślna to ręczna.<br><br>Deklaruje, że podajesz identyfikator zakresu usługi Device Provisioning Service (DPS) i Identyfikator rejestracji urządzenia, aby zapewnić obsługę administracyjną.  |
| **DeviceConnectionString** | Parametry połączenia z urządzenia IoT Edge zarejestrowane w IoT Hub, w pojedynczym cudzysłowie | **Wymagane** do instalacji ręcznej. Jeśli nie podano parametrów połączenia w parametrach skryptu, zostanie wyświetlony monit o podanie jednej podczas instalacji. |
| **Identyfikatora zakresu** | Identyfikator zakresu z wystąpienia usługi Device Provisioning skojarzonej z IoT Hubem. | **Wymagane** na potrzeby instalacji usługi DPS. Jeśli nie podano identyfikatora zakresu w parametrach skryptu, zostanie wyświetlony monit o podanie jednej podczas instalacji. |
| **Identyfikator rejestracji** | Identyfikator rejestracji generowany przez urządzenie | **Wymagane** na potrzeby instalacji usługi DPS. Jeśli nie podano identyfikatora rejestracji w parametrach skryptu, zostanie wyświetlony monit o podanie jednej podczas instalacji. |
| **SymmetricKey** | Klucz symetryczny używany do inicjowania obsługi tożsamości urządzenia IoT Edge podczas korzystania z usługi DPS | **Wymagane** do instalacji usługi DPS w przypadku korzystania z zaświadczania klucza symetrycznego. |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | Identyfikator URI obrazu agenta IoT Edge | Domyślnie Nowa instalacja IoT Edge używa najnowszego znacznika stopniowego dla obrazu agenta IoT Edge. Użyj tego parametru, aby ustawić określony tag dla wersji obrazu lub podać własny obraz agenta. Aby uzyskać więcej informacji, zobacz [Omówienie tagów IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwa użytkownika** | Nazwa użytkownika rejestru kontenerów | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj nazwę użytkownika z dostępem do rejestru. |
| **Hasło** | Bezpieczny ciąg hasła | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj hasło, aby uzyskać dostęp do rejestru. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Akceptowane wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono żadnego systemu operacyjnego kontenera, system Windows jest wartością domyślną. W przypadku kontenerów systemu Windows aparat kontenera zostanie uwzględniony w instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **RestartIfNeeded** | Dawaj | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Akceptowane wartości | Komentarze |
| --------- | --------------- | -------- |
| **Moc** | Dawaj | Ta flaga wymusza dezinstalację w przypadku, gdy poprzednia próba odinstalowania zakończyła się niepowodzeniem. 
| **RestartIfNeeded** | Dawaj | Ta flaga umożliwia skryptowi dezinstalacji ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
