---
title: Instalowanie usługi Azure IoT Edge w systemie Windows | Dokumenty firmy Microsoft
description: Instrukcje instalacji usługi Azure IoT Edge w systemach Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113457"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Install the Azure IoT Edge runtime on Windows (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemie Windows)

Środowisko uruchomieniowe usługi Azure IoT Edge przekształca urządzenie w urządzenie usługi IoT Edge. Środowisko wykonawcze można wdrożyć na urządzeniach tak małych jak Raspberry Pi lub tak dużych jak serwer przemysłowy. Gdy urządzenie jest skonfigurowane ze środowiska wykonawczego usługi IoT Edge, można rozpocząć wdrażanie logiki biznesowej do niego z chmury.

Aby dowiedzieć się więcej o środowiskou uruchomieniowym usługi IoT Edge, zobacz [Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury.](iot-edge-runtime.md)

W tym artykule wymieniono kroki instalowania środowiska wykonawczego usługi Azure IoT Edge w systemie Windows x64 (AMD/Intel) przy użyciu kontenerów systemu Windows.

> [!NOTE]
> Znany problem z systemem operacyjnym Windows zapobiega przejściu do stanu uśpienia i hibernacji zasilania, gdy są uruchomione moduły usługi IoT Edge (kontenery systemu Windows Nano Server izolowane przez proces). Ten problem wpływa na żywotność baterii urządzenia.
>
> Aby obejść ten problem, `Stop-Service iotedge` użyj polecenia, aby zatrzymać wszystkie uruchomione moduły usługi IoT Edge przed użyciem tych stanów zasilania.

Używanie kontenerów systemu Linux w systemach Windows nie jest zalecaną lub obsługiwaną konfiguracją produkcyjną dla usługi Azure IoT Edge. Jednak może być używany do celów rozwoju i testowania. Aby dowiedzieć się więcej, zobacz [Uruchamianie kontenerów systemu Linux za pomocą usługi IoT Edge w systemie Windows](how-to-install-iot-edge-windows-with-linux.md).

Aby uzyskać informacje o tym, co znajduje się w najnowszej wersji usługi IoT Edge, zobacz [wersje usługi Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja służy do sprawdzenia, czy urządzenie z systemem Windows może obsługiwać usługi IoT Edge i przygotować je do aparatu kontenera przed instalacją.

### <a name="supported-windows-versions"></a>Obsługiwane wersje systemu Windows

IoT Edge dla Windows wymaga systemu Windows w wersji 1809/build 17763, który jest najnowszą [długoterminową kompilacją pomocy technicznej systemu Windows](https://docs.microsoft.com/windows/release-information/). W przypadku obsługi jednostek SKU systemu Windows zobacz, co jest obsługiwane w zależności od tego, czy przygotowujesz się do scenariuszy produkcyjnych, czy scenariuszy rozwoju i testowania:

* **Produkcja:** Aby uzyskać najnowsze informacje na temat systemów operacyjnych, które są obecnie obsługiwane w scenariuszach produkcyjnych, zobacz [Systemy obsługiwane przez usługę Azure IoT Edge](support.md#operating-systems).
* **Programować i testować:** W scenariuszach deweloperskich i testowych usługa Azure IoT Edge z kontenerami systemu Windows może być zainstalowana w dowolnej wersji systemu Windows 10 lub Windows Server 2019 obsługującej funkcję kontenerów.

Urządzenia IoT Core muszą zawierać opcjonalną funkcję kontenerów systemu Windows IoT Core do obsługi środowiska wykonawczego usługi IoT Edge. Użyj następującego polecenia w [zdalnej sesji programu PowerShell,](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) aby sprawdzić, czy kontenery systemu Windows są obsługiwane na urządzeniu:

```powershell
Get-Service vmcompute
```

Jeśli usługa jest obecna, należy uzyskać pomyślną odpowiedź ze stanem usługi wymienionym jako **uruchomiony**. Jeśli `vmcompute` usługa nie zostanie znaleziona, urządzenie nie spełnia wymagań dotyczących usługi IoT Edge. Skontaktuj się z dostawcą sprzętu, aby zapytać o pomoc techniczną dla tej funkcji.

### <a name="prepare-for-a-container-engine"></a>Przygotowanie do pracy z silnikiem kontenerowym

Usługa Azure IoT Edge opiera się na silniku kontenera [zgodnego z OCI.](https://www.opencontainers.org/) W scenariuszach produkcyjnych użyj aparatu Moby dołączonego do skryptu instalacyjnego, aby uruchomić kontenery systemu Windows na urządzeniu z systemem Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalowanie usługi IoT Edge na nowym urządzeniu

>[!NOTE]
>Pakiety oprogramowania usługi Azure IoT Edge podlegają postanowieniu licencyjnym znajdującym się w pakietach (w katalogu LICENSE). Prosimy o zapoznanie się z postanowieniami licencyjne przed użyciem pakietu. Instalacja i korzystanie z pakietu oznacza akceptację niniejszych warunków. Jeśli nie zgadzasz się z postanowieniami licencyjnymi, nie korzystaj z pakietu.

Skrypt programu PowerShell pobiera i instaluje demona zabezpieczeń usługi Azure IoT Edge. Demon zabezpieczeń następnie uruchamia pierwszy z dwóch modułów środowiska uruchomieniowego, agenta usługi IoT Edge, który umożliwia zdalne wdrażanie innych modułów.

>[!TIP]
>W przypadku urządzeń IoT Core zaleca się uruchamianie poleceń instalacji przy użyciu sesji RemotePowerShell. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu PowerShell dla systemu Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Po zainstalowaniu środowiska wykonawczego usługi IoT Edge po raz pierwszy na urządzeniu należy aprowizować urządzenie z tożsamością z centrum IoT hub. Pojedyncze urządzenie usługi IoT Edge można aprowizować ręcznie przy użyciu ciągu połączenia urządzenia dostarczonego przez centrum IoT Hub. Możesz też użyć usługi aprowizacji urządzeń (DPS) do automatycznego aprowizowania urządzeń, co jest przydatne, gdy masz wiele urządzeń do skonfigurowania. W zależności od wyboru inicjowania obsługi administracyjnej wybierz odpowiedni skrypt instalacyjny.

W poniższych sekcjach opisano typowe przypadki użycia i parametry skryptu instalacyjnego i skryptu instalacyjnego ioT Edge na nowym urządzeniu.

### <a name="option-1-install-and-manually-provision"></a>Opcja 1: Instalacja i ręczne udostępnianie

W tej pierwszej opcji należy podać **parametry połączenia urządzenia** generowane przez Centrum IoT do aprowizowania urządzenia.

W tym przykładzie pokazano ręczną instalację z kontenerami systemu Windows:

1. Jeśli jeszcze tego nie zrobiłeś, zarejestruj nowe urządzenie Usługi IoT Edge i pobierz **ciąg połączenia urządzenia**. Skopiuj parametry połączenia, aby użyć go w dalszej części tej sekcji. Ten krok można wykonać za pomocą następujących narzędzi:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji programu PowerShell dla systemu AMD64, aby zainstalować usługę IoT Edge, a nie program PowerShell (x86). Jeśli nie masz pewności, którego typu sesji używasz, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby i środowisko wykonawcze usługi IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. W tym momencie urządzenia IoT Core mogą zostać automatycznie ponownie uruchomione. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, uruchom program PowerShell jako administrator ponownie.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi administracyjnej za pomocą kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w kroku 1. Ciąg połączenia urządzenia kojarzy urządzenie fizyczne z identyfikatorem urządzenia w centrum IoT Hub.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać cudzysłowów:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Aby sprawdzić stan aplikacji IoT Edge na urządzeniu, należy wykonać czynności w obszarze [Weryfikowanie pomyślnej instalacji.](#verify-successful-installation)

Podczas ręcznego instalowania i aprowizowania urządzenia można użyć dodatkowych parametrów, aby zmodyfikować instalację, w tym:

* Bezpośredni ruch, aby przejść przez serwer proxy
* Wskaż instalatora do katalogu trybu offline
* Deklarowanie określonego obrazu kontenera agenta i poświadczanie poświadczeń, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź do przodu, aby dowiedzieć się więcej o [wszystkich parametrach instalacji](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalacja i automatyczne udostępnianie

W tej drugiej opcji aprowizowanie urządzenia przy użyciu usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub. Podaj **identyfikator zakresu** z wystąpienia usługi inicjowania obsługi administracyjnej urządzeń wraz z wszelkimi innymi informacjami specyficznymi dla preferowanego [mechanizmu zaświadczania:](../iot-dps/concepts-security.md#attestation-mechanism)

* [Tworzenie i udostępnianie symulowanego urządzenia Usługi IoT Edge za pomocą wirtualnego modułu TPM w systemie Windows](how-to-auto-provision-simulated-device-windows.md)
* [Tworzenie i udostępnianie symulowanego urządzenia Usługi IoT Edge przy użyciu certyfikatów X.509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i inicjowania obsługi administracyjnej urządzenia usługi IoT Edge przy użyciu zaświadczania klucza symetrycznego](how-to-auto-provision-symmetric-keys.md)

Po zainstalowaniu i zainicjowaniu instalacji urządzenia można użyć dodatkowych parametrów, aby zmodyfikować instalację, w tym:

* Bezpośredni ruch, aby przejść przez serwer proxy
* Wskaż instalatora do katalogu trybu offline
* Deklarowanie określonego obrazu kontenera agenta i poświadczanie poświadczeń, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, kontynuuj czytanie tego artykułu lub pomiń, aby dowiedzieć się więcej o [wszystkich parametrach instalacji](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalacja w trybie offline lub określonej wersji

Podczas instalacji pobierane są trzy pliki:

* Skrypt programu PowerShell zawierający instrukcje instalacji
* Kabina Usługi Microsoft Azure IoT Edge zawierająca demon zabezpieczeń usługi IoT Edge (iotedged), silnik kontenerów Moby i moby CLI
* Instalator pakietu redystrybucyjnego programu Visual C++ (VC runtime)

Jeśli urządzenie będzie w trybie offline podczas instalacji lub jeśli chcesz zainstalować określoną wersję usługi IoT Edge, możesz pobrać te pliki z wyprzedzeniem na urządzenie. Gdy nałożona jest instalacja, skieruj skrypt instalacyjny do katalogu zawierającego pobrane pliki. Instalator najpierw sprawdza ten katalog, a następnie pobiera tylko składniki, które nie zostały znalezione. Jeśli wszystkie pliki są dostępne w trybie offline, można zainstalować bez połączenia z Internetem.

Można również użyć parametru ścieżki instalacji w trybie offline, aby zaktualizować usługę IoT Edge. Aby uzyskać więcej informacji, zobacz [Aktualizowanie demona zabezpieczeń i środowiska wykonawczego usługi IoT Edge](how-to-update-iot-edge.md).

1. Aby zapoznać się z najnowszymi plikami instalacyjnymi usługi IoT Edge wraz z poprzednimi wersjami, zobacz [Wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Znajdź wersję, którą chcesz zainstalować, i pobierz następujące pliki z sekcji **Zasoby** informacji o wersji na urządzenie IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z wersji 1.0.9 lub nowszych lub Microsoft-Azure-IoTEdge.cab z wersji 1.0.8 i starszych.

   Microsoft-Azure-IotEdge-arm32.cab jest również dostępny od 1.0.9 tylko do celów testowych. Usługa IoT Edge nie jest obecnie obsługiwana na urządzeniach z systemem Windows ARM32.

   Ważne jest, aby używać skryptu programu PowerShell z tej samej wersji co plik cab, którego używasz, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

3. Jeśli pobrany plik cab zawiera sufiks architektury, zmień nazwę pliku na **tylko Microsoft-Azure-IoTEdge.cab**.

4. Opcjonalnie pobierz instalatora dla programu Visual C++ redystrybucyjnego. Na przykład skrypt programu PowerShell używa tej wersji: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Zapisz instalatora w tym samym folderze na urządzeniu IoT co pliki usługi IoT Edge.

5. Aby zainstalować ze składnikami w trybie offline, [kropka źródła](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) lokalnej kopii skryptu programu PowerShell. Następnie użyj `-OfflineInstallationPath` parametru jako `Deploy-IoTEdge` części polecenia i podaj ścieżkę bezwzględną do katalogu plików. Na przykład:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Polecenie wdrażania użyje wszystkich składników znalezionych w dostarczonym katalogu plików lokalnych. Jeśli brakuje pliku cab lub instalatora języka Visual C++, spróbuje je pobrać.

6. Uruchom `Initialize-IoTEdge` polecenie, aby aprowizować urządzenie z tożsamością w Centrum IoT Hub. Podaj parametry połączenia urządzenia do ręcznego inicjowania obsługi administracyjnej lub wybierz jedną z metod opisanych w poprzedniej sekcji [automatycznego aprowizowania.](#option-2-install-and-automatically-provision)

   Jeśli urządzenie zostało ponownie `Deploy-IoTEdge`uruchomione po uruchomieniu , kropka `Initialize-IoTEdge`źródła skryptu programu PowerShell ponownie przed uruchomieniem .

Aby uzyskać więcej informacji na temat opcji instalacji w trybie offline, przejdź do przodu, aby dowiedzieć się więcej o [wszystkich parametrach instalacji](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Sprawdź stan usługi IoT Edge. Powinien być wymieniony jako uruchomiony.  

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut. Jeśli właśnie skończyłeś instalowanie środowiska uruchomieniowego IoT Edge, może zostać wyświetlona lista błędów z czasów między **uruchomieniem deploy-IoTEdge** i **Initialize-IoTEdge**. Te błędy są oczekiwane, ponieważ usługa próbuje uruchomić przed skonfigurowaniem.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Uruchom automatyczne sprawdzanie najczęściej występujących błędów konfiguracji i sieci.

```powershell
iotedge check
```

Lista uruchomionych modułów. Po nowej instalacji, jedynym modułem, który powinien zobaczyć uruchomiony jest **edgeAgent**. Po [pierwszym wdrożeniu modułów usługi IoT Edge](how-to-deploy-modules-portal.md) na urządzeniu zostanie uruchomiony również inny moduł systemowy **edgeHub.**

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Zarządzanie kontenerami modułów

Usługa IoT Edge wymaga aparatu kontenera działającego na urządzeniu. Podczas wdrażania modułu na urządzeniu środowisko uruchomieniowe usługi IoT Edge używa aparatu kontenera do ściągania obrazu kontenera z rejestru w chmurze. Usługa usługi Usługi Usługi IoT Edge umożliwia interakcję z modułami i pobieranie dzienników, ale czasami można użyć aparatu kontenera do interakcji z samym kontenerem.

Aby uzyskać więcej informacji na temat koncepcji modułu, zobacz [Opis modułów usługi Azure IoT Edge](iot-edge-modules.md).

Jeśli korzystasz z kontenerów systemu Windows na urządzeniu Usługi IoT Edge systemu Windows, instalacja usługi IoT Edge zawierała aparat kontenera Moby. Silnik Moby był oparty na tych samych standardach co docker i został zaprojektowany do pracy równolegle na tym samym komputerze co Docker Desktop. Z tego powodu, jeśli chcesz kierować kontenery zarządzane przez aparat Moby, należy specjalnie kierować tego aparatu zamiast platformy Docker.

Na przykład, aby wyświetlić listę wszystkich obrazów platformy Docker, użyj następującego polecenia:

```powershell
docker images
```

Aby wyświetlić listę wszystkich obrazów Moby, zmodyfikuj to samo polecenie za pomocą wskaźnika do aparatu Moby:To list all Moby images, modify the same command with a pointer to the Moby engine:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identyfikator URI aparatu jest wymieniony w danych wyjściowych skryptu instalacyjnego lub można go znaleźć w sekcji ustawień środowiska uruchomieniowego kontenera dla pliku config.yaml.

![moby_runtime uri w config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Aby uzyskać więcej informacji na temat poleceń, których można używać do interakcji z kontenerami i obrazami uruchomionymi na urządzeniu, zobacz [Interfejsy wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Odinstalowywanie przeglądarce IoT Edge

Jeśli chcesz usunąć instalację usługi IoT Edge z urządzenia z systemem Windows, użyj następującego polecenia z administracyjnego okna programu PowerShell. To polecenie usuwa środowisko uruchomieniowe usługi IoT Edge wraz z istniejącą konfiguracją i danymi aparatu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Polecenie Odinstaluj IoTEdge nie działa w systemie Windows IoT Core. Aby usunąć ioT Edge z urządzeń Windows IoT Core, musisz ponownie wdrożyć obraz IoT Core systemu Windows.

Aby uzyskać więcej informacji o opcjach dezinstalacji, użyj polecenia `Get-Help Uninstall-IoTEdge -full`.

## <a name="verify-installation-script"></a>Weryfikowanie skryptu instalacyjnego

Polecenia instalacyjne podane w tym artykule używają polecenia cmdlet Invoke-WebRequest, aby zażądać skryptu instalacyjnego od `aka.ms/iotedge-win`pliku . Ten link wskazuje`IoTEdgeSecurityDaemon.ps1` skrypt z najnowszej [wersji IoT Edge](https://github.com/Azure/azure-iotedge/releases). Można również pobrać ten skrypt lub wersję skryptu z określonej wersji, aby uruchomić polecenia instalacyjne na urządzeniu Z usługą IoT Edge.

Dostarczony skrypt jest podpisany w celu zwiększenia bezpieczeństwa. Podpis można zweryfikować, pobierając skrypt na urządzenie, a następnie uruchamiając następujące polecenie programu PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stan danych **wyjściowych jest Prawidłowy,** jeśli podpis jest zweryfikowany.

## <a name="all-installation-parameters"></a>Wszystkie parametry instalacji

W poprzednich sekcjach wprowadzono typowe scenariusze instalacji z przykładami użycia parametrów do modyfikowania skryptu instalacji. Ta sekcja zawiera tabele odwołań typowych parametrów używanych do instalowania, aktualizowania lub odinstalowywania usługi IoT Edge.

### <a name="deploy-iotedge"></a>Wdrażanie-IoTEdge

Polecenie Deploy-IoTEdge pobiera i wdraża demona zabezpieczeń IoT Edge i jego zależności. Polecenie wdrażania akceptuje te typowe parametry, między innymi. Aby uzyskać pełną listę, `Get-Help Deploy-IoTEdge -full`użyj polecenia .  

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Konteneros** | **Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows usługa IoT Edge używa aparatu kontenerów moby dołączonych do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenerów przed rozpoczęciem instalacji. |
| **Proxy** | Proxy URL | Dołącz ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby dotrzeć do Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **Ścieżka instalacji w trybie offline** | Ścieżka katalogu | Jeśli ten parametr jest dołączony, instalator sprawdzi wymieniony katalog dla kabiny usługi IoT Edge i plików MSI środowiska wykonawczego VC wymaganych do instalacji. Wszystkie pliki, których nie znaleziono w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować usługę IoT Edge bez połączenia z Internetem. Ten parametr można również użyć do użycia określonej wersji. |
| **InvokeWebRequestParameters** | Ilość skrótów parametrów i wartości | Podczas instalacji jest składanych kilku żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skrypt wdrożenia, aby ponownie uruchomić komputer bez monitowania, jeśli to konieczne. |

### <a name="initialize-iotedge"></a>Inicjowanie-IoTEdge

Polecenie Initialize-IoTEdge konfiguruje urządzenie IoT Edge z ciągiem połączenia urządzenia i szczegółami operacyjnymi. Większość informacji generowanych przez to polecenie jest następnie przechowywana w pliku iotedge\config.yaml. Polecenie inicjowania akceptuje te typowe parametry, między innymi. Aby uzyskać pełną listę, `Get-Help Initialize-IoTEdge -full`użyj polecenia .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Ręcznie** | Brak | **Przełącznik parametru**. Jeśli nie określono typu inicjowania obsługi administracyjnej, ręczna jest wartością domyślną.<br><br>Deklaruje, że podasz parametry połączenia urządzenia, aby ręcznie aprowizować urządzenie |
| **Dps** | Brak | **Przełącznik parametru**. Jeśli nie określono typu inicjowania obsługi administracyjnej, ręczna jest wartością domyślną.<br><br>Deklaruje, że użytkownik udostępnia identyfikator zakresu usługi aprowizacji urządzeń (DPS) oraz identyfikator rejestracyjny urządzenia w celu udostępnienia go za pośrednictwem usługi DPS.  |
| **Połączenie urządzenia** | Parametry połączenia z urządzenia usługi IoT Edge zarejestrowanego w centrum IoT Hub w cudzysłowie | **Wymagane** do ręcznego inicjowania obsługi administracyjnej. Jeśli nie podasz parametry połączenia w parametrach skryptu, zostanie wyświetlony monit o jeden. |
| **Idzakresu** | Identyfikator zakresu z wystąpienia usługi inicjowania obsługi administracyjnej urządzeń skojarzonej z Centrum IoT Hub. | **Wymagane** do inicjowania obsługi administracyjnej DPS. Jeśli nie podasz identyfikatora zakresu w parametrach skryptu, zostanie wyświetlony monit o jeden. |
| **Identyfikator rejestracji** | Identyfikator rejestracji wygenerowany przez urządzenie | **Wymagane** do inicjowania obsługi administracyjnej DPS, jeśli używasz tpm lub zaświadczania klucza symetrycznego. **Opcjonalnie** w przypadku korzystania z zaświadczenia o certyfikatach X.509. |
| **X509IdentityCertificate** | Ścieżka identyfikatora URI do certyfikatu tożsamości urządzenia X.509 na urządzeniu. | **Wymagane** do inicjowania obsługi administracyjnej DPS, jeśli używasz zaświadczania certyfikatu X.509. |
| **X509IdentityPrivateKey** | Ścieżka identyfikatora URI do klucza certyfikatu tożsamości urządzenia X.509 na urządzeniu. | **Wymagane** do inicjowania obsługi administracyjnej DPS, jeśli używasz zaświadczania certyfikatu X.509. |
| **Symmetrickey** | Klucz symetryczny używany do aprowizowania tożsamości urządzenia usługi IoT Edge podczas korzystania z dps | **Wymagane** do inicjowania obsługi administracyjnej DPS, jeśli przy użyciu zaświadczania klucza symetrycznego. |
| **Konteneros** | **Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows usługa IoT Edge używa aparatu kontenerów moby dołączonych do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenerów przed rozpoczęciem instalacji. |
| **InvokeWebRequestParameters** | Ilość skrótów parametrów i wartości | Podczas instalacji jest składanych kilku żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **AgentImage (AgentImage)** | Identyfikator URI agenta ioT Edge | Domyślnie nowa instalacja IoT Edge używa najnowszego tagu stopniowego dla obrazu agenta IoT Edge. Ten parametr służy do ustawiania określonego znacznika dla wersji obrazu lub do dostarczania własnego obrazu agenta. Aby uzyskać więcej informacji, zobacz [Opis tagów IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwę użytkownika** | Nazwa użytkownika rejestru kontenerów | Tego parametru należy używać tylko po ustawieniu parametru -AgentImage na kontener w rejestrze prywatnym. Podaj nazwę użytkownika z dostępem do rejestru. |
| **Hasło** | Ciąg bezpiecznego hasła | Tego parametru należy używać tylko po ustawieniu parametru -AgentImage na kontener w rejestrze prywatnym. Podaj hasło, aby uzyskać dostęp do rejestru. |

### <a name="update-iotedge"></a>Aktualizacja-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Konteneros** | **Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, windows jest wartością domyślną. W przypadku kontenerów systemu Windows aparat kontenerów zostanie dołączony do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenerów przed rozpoczęciem instalacji. |
| **Proxy** | Proxy URL | Dołącz ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby dotrzeć do Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Ilość skrótów parametrów i wartości | Podczas instalacji jest składanych kilku żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **Ścieżka instalacji w trybie offline** | Ścieżka katalogu | Jeśli ten parametr jest dołączony, instalator sprawdzi wymieniony katalog dla kabiny usługi IoT Edge i plików MSI środowiska wykonawczego VC wymaganych do instalacji. Wszystkie pliki, których nie znaleziono w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować usługę IoT Edge bez połączenia z Internetem. Ten parametr można również użyć do użycia określonej wersji. |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skrypt wdrożenia, aby ponownie uruchomić komputer bez monitowania, jeśli to konieczne. |

### <a name="uninstall-iotedge"></a>Odinstaluj-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Życie** | brak | Ta flaga wymusza dezinstalację w przypadku, gdy poprzednia próba odinstalowania nie powiodła się.
| **RestartIfNeeded** | brak | Ta flaga umożliwia skrypt odinstalowywania, aby ponownie uruchomić komputer bez monitowania, jeśli to konieczne. |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie IoT Edge aprowizowane z zainstalowanym czasem wykonywania, można [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z prawidłową instalacją aplikacji IoT Edge, zapoznaj się ze stroną [rozwiązywania problemów.](troubleshoot.md)

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska wykonawczego](how-to-update-iot-edge.md).
