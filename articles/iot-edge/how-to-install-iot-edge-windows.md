---
title: Instalowanie usługi Azure IoT Edge na Windows | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje instalacji w systemie Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: f67f24cab907c3fe9998704e0a0a85d5b29f60a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808866"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat środowiska uruchomieniowego usługi IoT Edge, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na usługi Windows x64 (AMD/Intel) systemu przy użyciu kontenerów Windows.

> [!NOTE]
> Znany problem systemu operacyjnego Windows uniemożliwia przejścia w tryb uśpienia i hibernacji stany zasilania, gdy są uruchomione moduły usługi IoT Edge (izolowany proces kontenery Windows Nano Server). Ten problem ma wpływ na czas pracy baterii urządzenia.
>
> Obejść ten problem, użyj polecenia `Stop-Service iotedge` można zatrzymać wszystkie uruchomione moduły usługi IoT Edge, przed rozpoczęciem korzystania z tych stanów zasilania. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Używanie kontenerów systemu Linux w systemach Windows nie jest produkcyjnych zalecane lub obsługiwanych konfiguracji dla usługi Azure IoT Edge. Jednak może służyć do tworzenia i testowania. Aby dowiedzieć się więcej, zobacz [korzystanie z usługi IoT Edge na Windows umożliwiające uruchamianie kontenerów systemu Linux](how-to-install-iot-edge-windows-with-linux.md).

Aby dowiedzieć się, jak to, co jest uwzględnione w najnowszej wersji usługi IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Wymagania wstępne

Użyj tej sekcji, aby sprawdzić, czy urządzenie Windows może obsługiwać usługi IoT Edge i przygotować go do aparatu kontenera, przed rozpoczęciem instalacji. 

### <a name="supported-windows-versions"></a>Obsługiwane wersje Windows

W przypadku projektowania i testowania scenariuszy usługi Azure IoT Edge przy użyciu kontenerów Windows można zainstalować w dowolnej wersji systemu Windows 10 lub Windows Server 2019 r (kompilacja 17763) obsługującej funkcję kontenerów. Aby uzyskać informacje o tym, które systemy operacyjne są obecnie obsługiwane na potrzeby scenariuszy produkcyjnych, zobacz [usługi Azure IoT Edge obsługiwane systemy](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Przygotowanie do aparatu kontenera 

Usługa Azure IoT Edge opiera się na [zgodnego z OCI](https://www.opencontainers.org/) kontenera aparatu. Na potrzeby scenariuszy produkcyjnych umożliwia uruchamianie kontenerów Windows na urządzeniu z systemem Windows aparatu Moby uwzględnione w skrypcie instalacji. 

## <a name="install-iot-edge-on-a-new-device"></a>Zainstaluj usługi IoT Edge na nowe urządzenie

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Skrypt programu PowerShell pobiera i instaluje demona zabezpieczeń usługi Azure IoT Edge. Demon zabezpieczeń następnie uruchamia pierwsze dwa moduły środowiska uruchomieniowego, agent usługi IoT Edge, który umożliwia używanie wdrożeń zdalnego innych modułów. 

Po zainstalowaniu środowiska uruchomieniowego usługi IoT Edge po raz pierwszy na urządzeniu, musisz aprowizować urządzenia przy użyciu tożsamości z usługi IoT hub. Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń, aby skonfigurować. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

Poniższe sekcje opisują typowe przypadki użycia i Parametry skryptu instalacji usługi IoT Edge na nowym urządzeniu. 

### <a name="option-1-install-and-manually-provision"></a>Opcja 1: Instalacja oraz ręcznie zainicjować obsługę administracyjną

W przypadku tej opcji pierwszy podasz **parametry połączenia urządzenia** generowane przez usługę IoT Hub, aprowizować urządzenie. 

W tym przykładzie przedstawiono ręcznej instalacji przy użyciu kontenerów Windows:

1. Jeśli jeszcze nie, zarejestrować nowe urządzenie usługi IoT Edge i pobieranie **parametry połączenia urządzenia**. Skopiuj parametry połączenia do użycia w przyszłości w tej sekcji. Możesz wykonać ten krok za pomocą następujących narzędzi:

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

3. **IoTEdge Wdróż** polecenie sprawdza, czy komputer Windows jest w obsługiwanej wersji, włączenie funkcji kontenerów i pliki do pobrania moby środowiska uruchomieniowego i środowisko uruchomieniowe usługi IoT Edge. Domyślnie polecenia przy użyciu kontenerów Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. W tym momencie urządzenia IoT Core może automatycznie uruchomiony ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server może monitować użytkownika o ponowne uruchomienie. Jeśli tak, teraz ponownie uruchomić urządzenie. Gdy urządzenie jest gotowe, ponownie uruchom program PowerShell jako administrator.

5. **IoTEdge zainicjować** polecenie konfiguruje środowisko uruchomieniowe usługi IoT Edge na urządzeniu. Polecenie domyślne z ręcznego inicjowania obsługi administracyjnej za pomocą kontenerów Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia, do którego został pobrany w kroku 1. Parametry połączenia urządzenia kojarzy urządzenia fizycznego identyfikator urządzenia w usłudze IoT Hub. 

   Parametry połączenia urządzenia ma następujący format i nie może zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Użyj kroków w [Sprawdź pomyślna instalacja](#verify-successful-installation) Aby sprawdzić stan usługi IoT Edge na urządzeniu. 

Po zainstalowaniu i ręcznie aprowizowanie urządzenia można użyć dodatkowych parametrów do modyfikowania instalacji, w tym:
* Bezpośrednie kierowanie ruchu za pośrednictwem serwera proxy
* Wskaż Instalator w trybie offline katalogu
* Deklarowanie obraz kontenera określonego agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź do sekcji Dowiedz się więcej o [wszystkie parametry instalacji](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalowanie i automatycznie aprowizować

Ta druga opcja służy do aprowizacji urządzenia przy użyciu IoT Hub Device Provisioning Service. Podaj **identyfikator zakresu** z wystąpienia usługi Device Provisioning i **identyfikator rejestracji** na urządzeniu.

W poniższym przykładzie pokazano automatycznej instalacji, za pomocą kontenerów Windows:

1. Postępuj zgodnie z instrukcjami w [tworzenie i aprowizowanie symulowanego urządzenia TPM IoT Edge na Windows](how-to-auto-provision-simulated-device-windows.md) konfiguracji usługi Device Provisioning i pobrać jego **identyfikator zakresu**symulowane urządzenie TPM i pobrać jego **Identyfikator rejestracji**, następnie utworzyć rejestrację indywidualną. Gdy urządzenie jest zarejestrowane w usłudze IoT hub, kontynuuj te kroki instalacji.  

   >[!TIP]
   >Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania. 

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Aby zainstalować usługi IoT Edge, PowerShell (x86), należy użyć AMD64 sesji programu PowerShell. Jeśli nie masz pewności, jakiego typu sesji używasz, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **IoTEdge Wdróż** polecenie sprawdza, czy komputer Windows jest w obsługiwanej wersji, włączenie funkcji kontenerów i pliki do pobrania moby środowiska uruchomieniowego i środowisko uruchomieniowe usługi IoT Edge. Domyślnie polecenia przy użyciu kontenerów Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. W tym momencie urządzenia IoT Core może automatycznie uruchomiony ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server może monitować użytkownika o ponowne uruchomienie. Jeśli tak, teraz ponownie uruchomić urządzenie. Gdy urządzenie jest gotowe, ponownie uruchom program PowerShell jako administrator.

6. **IoTEdge zainicjować** polecenie konfiguruje środowisko uruchomieniowe usługi IoT Edge na urządzeniu. Polecenie domyślne z ręcznego inicjowania obsługi administracyjnej za pomocą kontenerów Windows. Użyj `-Dps` flagi do korzystania z usługi Device Provisioning zamiast ręcznego inicjowania obsługi administracyjnej.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Po wyświetleniu monitu podaj identyfikator zakresu z usługi Device Provisioning i identyfikator rejestracji na urządzeniu, oba powinny mieć pobranego w kroku 1.

8. Użyj kroków w [Sprawdź pomyślna instalacja](#verify-successful-installation) Aby sprawdzić stan usługi IoT Edge na urządzeniu. 

Po zainstalowaniu i ręcznie aprowizowanie urządzenia można użyć dodatkowych parametrów do modyfikowania instalacji, w tym:
* Bezpośrednie kierowanie ruchu za pośrednictwem serwera proxy
* Wskaż Instalator w trybie offline katalogu
* Deklarowanie obraz kontenera określonego agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przeczytaj ten artykuł lub pominąć, aby dowiedzieć się więcej na temat [wszystkie parametry instalacji](#all-installation-parameters).

## <a name="offline-installation"></a>Instalacja w trybie offline

Podczas instalacji są pobierane dwa pliki: 
* Cab Microsoft Azure IoT Edge zawiera demona zabezpieczeń usługi IoT Edge (iotedged), aparat container Moby i Moby interfejsu wiersza polecenia.
* Visual C++ pakiet redystrybucyjny (środowisko uruchomieniowe VC) msi

Możesz pobrać jeden lub oba te pliki wcześniej na urządzeniu, a następnie punktu skrypt instalacji w katalogu, który zawiera pliki. Instalator sprawdza, czy katalog najpierw, a następnie pobiera tylko składniki, które nie zostaną znalezione. Jeśli wszystkie pliki są dostępne w trybie offline, można zainstalować bez połączenia internetowego. Ta funkcja umożliwia także instalowanie określonej wersji składników.  

Aby uzyskać najnowsze pliki instalacyjne usługi IoT Edge wraz z poprzednich wersji, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Aby zainstalować przy użyciu składników w trybie offline, należy użyć `-OfflineInstallationPath` parametru IoTEdge wdrażanie w ramach polecenia i podaj ścieżkę bezwzględną do katalogu plików. Na przykład:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Umożliwia także parametr ścieżki instalacji w trybie offline przy użyciu polecenia Update-IoTEdge, wprowadzona w dalszej części tego artykułu. 

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Sprawdź stan usługi IoT Edge. Powinny być wymienione jako uruchomiony.  

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut. Jeśli użytkownik po prostu zakończył instalowanie środowiska uruchomieniowego usługi IoT Edge, może wyświetlić listę błędów od czasu między uruchomionymi **IoTEdge Wdróż** i **IoTEdge zainicjować**. Te błędy są oczekiwane zachowanie, ponieważ usługa próbuje uruchomić przed konfigurowany. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Utwórz listę uruchomionych modułów. Po zainstalowaniu nowego tylko moduł powinien zostać wyświetlony jest uruchomiona **edgeAgent**. Po zakończeniu [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md), widoczne będą inne. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Zarządzanie kontenerami modułu

Usługa IoT Edge wymaga aparatu kontenera, na urządzeniu z. Podczas wdrażania modułu na urządzeniu, środowisko uruchomieniowe usługi IoT Edge używa aparatu kontenera do ściągania obrazu kontenera z rejestru w chmurze. Usługa IoT Edge umożliwia interakcję z moduły, a następnie pobrać dzienniki, ale czasami możesz chcieć użyć aparatu kontenera do interakcji z samego kontenera. 

Aby uzyskać więcej informacji o pojęciach dotyczących modułu, zobacz [modułów Omówienie usługi Azure IoT Edge](iot-edge-modules.md). 

Jeśli korzystasz z kontenerów Windows na urządzeniu z systemem Windows IoT Edge, instalacji usługi IoT Edge uwzględnione Moby aparatu kontenera. Aparat Moby była oparta na te same standardy, jak Docker i zaprojektowano tak, aby uruchomić równolegle na tym samym komputerze co pulpitu platformy Docker. Z tego powodu aby kontenery docelowej zarządzana przez aparat Moby trzeba specjalnie docelowe silnika zamiast platformy Docker. 

Na przykład aby wyświetlić listę wszystkich obrazów platformy Docker, użyj następującego polecenia:

```powershell
docker images
```

Aby wyświetlić listę wszystkich obrazów Moby, zmodyfikuj samo polecenie za pomocą wskaźnika do aparatu Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Aparat, identyfikator URI znajduje się w danych wyjściowych skryptu instalacji, lub można znaleźć w sekcji Ustawienia środowiska uruchomieniowego kontenera dla pliku config.yaml. 

![uri moby_runtime w config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Aby uzyskać więcej informacji dotyczących poleceń, można użyć do interakcji z kontenerami i obrazy uruchomione na urządzeniu, zobacz [interfejsów z wierszem polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aktualizowanie istniejącej instalacji

Jeśli zostały już zainstalowane środowisko uruchomieniowe usługi IoT Edge na urządzeniu zanim i zaaprowizowane przy użyciu tożsamości z usługi IoT Hub, środowisko uruchomieniowe można aktualizować bez konieczności ponownego wprowadzania informacji urządzenia. 

Aby uzyskać więcej informacji, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).

W tym przykładzie przedstawiono instalację, który wskazuje istniejący plik konfiguracyjny i używa kontenerów Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Po zaktualizowaniu usługi IoT Edge służy dodatkowych parametrów do modyfikowania aktualizacji, w tym:
* Bezpośredni ruch za pośrednictwem serwera proxy, lub
* Wskaż Instalator w trybie offline katalogu 
* Ponowne uruchomienie bez wyświetlania monitu, jeśli to konieczne

Nie można zadeklarować obrazu kontenera agenta usługi IoT Edge Parametry skryptu, ponieważ te informacje jest już ustawiona w pliku konfiguracji z poprzedniej instalacji. Jeśli chcesz zmodyfikować agenta obrazu kontenera, to zrobić w pliku config.yaml. 

Aby uzyskać więcej informacji na temat tych aktualizacji opcji, należy użyć polecenia `Get-Help Update-IoTEdge -full` lub odwoływać się do [wszystkie parametry instalacji](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Odinstalowywanie usługi IoT Edge

Jeśli chcesz usunąć instalację usługi IoT Edge na urządzeniu Windows, użyj następującego polecenia z administracyjne okna programu PowerShell. To polecenie usuwa środowisko uruchomieniowe usługi IoT Edge wraz z istniejącej konfiguracji i danych aparatu Moby. 

```powershell
Uninstall-IoTEdge
```

Polecenie Odinstaluj IoTEdge nie działa w systemie Windows IoT Core. Aby usunąć usługi IoT Edge z urządzeniami Windows IoT Core, należy ponownie wdrożyć obraz systemu Windows IoT Core. 

Aby uzyskać więcej informacji o opcjach odinstalowywania, użyj polecenia `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Wszystkie parametry instalacji

Przedstawione w poprzednich sekcjach wprowadzono typowe scenariusze instalacji za pomocą przykładów dotyczących sposobów używania parametrów w celu zmodyfikowania skryptu instalacji. Ta sekcja zawiera tabele odwołań wspólnych parametrów używane, aby zainstalować, zaktualizować lub odinstalować usługi IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Polecenie Wdróż IoTEdge pliki do pobrania i wdraża demona zabezpieczeń IoT Edge i jego zależności. Polecenie wdrożenia akceptuje następujące typowe parametry, między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Deploy-IoTEdge -full`.  

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** lub **systemu Linux** | Jeśli określono bez systemu operacyjnego kontenera, Windows jest wartością domyślną.<br><br>Dla kontenerów Windows usługi IoT Edge korzysta z aparatu kontenera moby dostępny w instalacji. Dla kontenerów systemu Linux musisz zainstalować aparat container przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Ten parametr należy uwzględnić, jeśli urządzenie musi przechodzić przez serwer proxy do uzyskiwania dostępu do Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr jest dołączony, program instalacyjny sprawdzi wymienionych katalog dla plików MSI środowiska uruchomieniowego VC wymaganych do instalacji i cab usługi IoT Edge. Pobierane są wszystkie pliki, które nie znajduje się w katalogu. Jeśli oba pliki znajdują się w katalogu, możesz zainstalować usługi IoT Edge, bez połączenia z Internetem. Ten parametr umożliwia również użyć określonej wersji. |
| **InvokeWebRequestParameters** | Tablica skrótów parametrów i wartości | Podczas instalacji są wprowadzane kilka żądań sieci web. Użyj tego pola, aby ustawić parametry te żądania sieci web. Ten parametr jest przydatne skonfigurować poświadczenia dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Brak | Ta flaga umożliwia skryptu wdrażania, ponowne uruchomienie komputera bez wyświetlania monitu, jeśli to konieczne. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Polecenie IoTEdge zainicjować konfiguruje usługi IoT Edge przy użyciu parametrów połączenia urządzenia i informacje o operacjach. Wiele informacji generowanych przez to polecenie jest następnie przechowywany w pliku iotedge\config.yaml. Polecenie inicjowania akceptuje następujące typowe parametry, między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Initialize-IoTEdge -full`. 

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Ręcznie** | Brak | **Przełącz parametru**. Jeśli żaden typ inicjowania obsługi administracyjnej jest określony, ręczne jest wartością domyślną.<br><br>Deklaruje zapewni parametry połączenia urządzenia, aby aprowizować urządzenie ręcznie |
| **Usługa DPS** | Brak | **Przełącz parametru**. Jeśli żaden typ inicjowania obsługi administracyjnej jest określony, ręczne jest wartością domyślną.<br><br>Deklaruje zapewni urządzenia inicjowania obsługi usługi (DPS) identyfikator zakresu i identyfikator rejestracji urządzenia do aprowizacji za pośrednictwem usługi DPS.  |
| **DeviceConnectionString** | Parametry połączenia, z urządzenia usługi IoT Edge zarejestrowane w usłudze IoT Hub, w pojedynczym cudzysłowie | **Wymagane** dla instalacji ręcznej. Jeśli nie zostaną podane parametry połączenia w parametrach skrypt, użytkownik jest monitowany jednego podczas instalacji. |
| **ScopeId** | Identyfikator zakresu z wystąpienia usługi Device Provisioning skojarzonych z Twoim Centrum IoT Hub. | **Wymagane** instalacji punktu dystrybucji. Jeśli nie podasz Identyfikatora zakresu, w polu Parametry skryptu, użytkownik jest monitowany jednego podczas instalacji. |
| **RegistrationId** | Identyfikator rejestracji, generowane przez urządzenie | **Wymagane** instalacji punktu dystrybucji. Jeśli nie podasz identyfikator rejestracji, za pomocą parametrów skryptu, użytkownik jest monitowany jednego podczas instalacji. |
| **ContainerOs** | **Windows** lub **systemu Linux** | Jeśli określono bez systemu operacyjnego kontenera, Windows jest wartością domyślną.<br><br>Dla kontenerów Windows usługi IoT Edge korzysta z aparatu kontenera moby dostępny w instalacji. Dla kontenerów systemu Linux musisz zainstalować aparat container przed rozpoczęciem instalacji. |
| **InvokeWebRequestParameters** | Tablica skrótów parametrów i wartości | Podczas instalacji są wprowadzane kilka żądań sieci web. Użyj tego pola, aby ustawić parametry te żądania sieci web. Ten parametr jest przydatne skonfigurować poświadczenia dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | Identyfikator URI obrazu agenta usługi IoT Edge | Domyślnie nowa instalacja usługi IoT Edge taga najnowsza wersja stopniowe obraz agenta usługi IoT Edge. Użyj tego parametru można ustawić określony tag wersji obrazu lub w celu udostępnienia swój własny obraz agenta. Aby uzyskać więcej informacji, zobacz [tagi usługi IoT Edge zrozumieć](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwa użytkownika** | Nazwa użytkownika rejestru kontenerów | Użyj tego parametru, tylko wtedy, gdy parametr - AgentImage jest ustawiony do kontenera w rejestrze prywatnym. Należy podać nazwę użytkownika z dostępem do rejestru. |
| **Hasło** | Ciąg bezpieczne hasło | Użyj tego parametru, tylko wtedy, gdy parametr - AgentImage jest ustawiony do kontenera w rejestrze prywatnym. Podaj hasło dostępu do rejestru. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** lub **systemu Linux** | Jeśli brak kontenera jest określana przez system operacyjny Windows jest wartością domyślną. Dla kontenerów Windows aparatu kontenera będzie dostępny w instalacji. Dla kontenerów systemu Linux musisz zainstalować aparat container przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Ten parametr należy uwzględnić, jeśli urządzenie musi przechodzić przez serwer proxy do uzyskiwania dostępu do Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tablica skrótów parametrów i wartości | Podczas instalacji są wprowadzane kilka żądań sieci web. Użyj tego pola, aby ustawić parametry te żądania sieci web. Ten parametr jest przydatne skonfigurować poświadczenia dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr jest dołączony, program instalacyjny sprawdzi wymienionych katalog dla plików MSI środowiska uruchomieniowego VC wymaganych do instalacji i cab usługi IoT Edge. Pobierane są wszystkie pliki, które nie znajduje się w katalogu. Jeśli oba pliki znajdują się w katalogu, możesz zainstalować usługi IoT Edge, bez połączenia z Internetem. Ten parametr umożliwia również użyć określonej wersji. |
| **RestartIfNeeded** | Brak | Ta flaga umożliwia skryptu wdrażania, ponowne uruchomienie komputera bez wyświetlania monitu, jeśli to konieczne. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Force** | Brak | Ta flaga wymusza dezinstalację, w przypadku, gdy poprzednia próba odinstalowania nie powiodło się. 
| **RestartIfNeeded** | Brak | Ta flaga umożliwia skryptu dezinstalacji, ponowne uruchomienie komputera bez wyświetlania monitu, jeśli to konieczne. |


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy z instalacją usługi IoT Edge prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).
