---
title: Instalowanie usługi Azure IoT Edge na Windows | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje instalacji w systemie Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: d669842c60fb69820e6d94ad0a9359f6460101fe
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481879"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze. 

Aby dowiedzieć się więcej na temat środowiska uruchomieniowego usługi IoT Edge, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na usługi Windows x64 (AMD/Intel) systemu. Obsługa Windows jest obecnie w wersji zapoznawczej.

> [!NOTE]
> Używanie kontenerów systemu Linux w systemach Windows nie jest produkcyjnych zalecane lub obsługiwanych konfiguracji dla usługi Azure IoT Edge. Jednak może służyć do tworzenia i testowania.

## <a name="prerequisites"></a>Wymagania wstępne

Użyj tej sekcji, aby sprawdzić, czy urządzenie Windows może obsługiwać usługi IoT Edge i przygotować go do aparatu kontenera, przed rozpoczęciem instalacji. 

### <a name="supported-windows-versions"></a>Obsługiwane wersje Windows

Usługa Azure IoT Edge obsługuje różne wersje systemu Windows, w zależności od tego, czy korzystasz z kontenerów Windows lub kontenerów systemu Linux. 

Najnowszą wersję usługi Azure IoT Edge przy użyciu kontenerów Windows można uruchomić w następujących wersjach systemu Windows:
* Windows 10 lub IoT Core z aktualizacją z października 2018 (kompilacja 17763)
* Windows Server 2019 (build 17763)

Najnowszą wersję usługi Azure IoT Edge z kontenerami systemu Linux można uruchomić w następujących wersjach systemu Windows: 
* Windows 10 rozliczenia aktualizacji (kompilacja 14393) lub nowszej
* Windows Server 2016 lub nowszy

Aby uzyskać więcej informacji o tym, które są obecnie obsługiwane systemy operacyjne, zobacz [pomocy technicznej usługi Azure IoT Edge](support.md#operating-systems). 

Aby uzyskać więcej informacji o to, co jest uwzględnione w najnowszej wersji usługi IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Przygotowanie do aparatu kontenera 

Usługa Azure IoT Edge opiera się na [zgodnego z OCI](https://www.opencontainers.org/) kontenera aparatu. Na potrzeby scenariuszy produkcyjnych umożliwia uruchamianie kontenerów Windows na urządzeniu z systemem Windows aparatu Moby uwzględnione w skrypcie instalacji. Do tworzenia i testowania, umożliwia uruchamianie kontenerów systemu Linux na urządzeniu z systemem Windows, ale musisz zainstalować i skonfigurować aparat container przed zainstalowaniem usługi IoT Edge. Dla dowolnego scenariusza zobacz następujące sekcje wstępnie wymaganych składników do przygotowywania urządzenia. 

Jeśli chcesz zainstalować usługi IoT Edge na maszynie wirtualnej, włączać wirtualizację zagnieżdżoną i przydziel co najmniej 2 GB pamięci RAM. Jak włączać wirtualizację zagnieżdżoną różni się w zależności od funkcji hypervisor użytkowanie. Dla funkcji Hyper-V maszyny wirtualne generacji 2 mają zagnieżdżone wirtualizacji włączona domyślnie. Dla oprogramowania VMWare Brak przełącznika, aby włączyć tę funkcję na maszynie wirtualnej. 

#### <a name="moby-engine-for-windows-containers"></a>Aparat Moby dla kontenerów Windows

W przypadku urządzeń Windows działa IoT Edge w scenariuszach produkcyjnych Moby to tylko aparat oficjalnie obsługiwane kontenerów. Skrypt instalacji automatycznie zainstaluje aparat Moby na urządzeniu z systemem, przed zainstalowaniem usługi IoT Edge. Przygotuj urządzenie, włączając funkcję kontenerów. 

1. Na pasku rozpoczęcia wyszukiwania **Windows Włącz lub wyłącz funkcje** , a następnie otwórz program Panelu sterowania.
2. Znajdź i zaznacz **kontenery**.
3. Kliknij przycisk **OK**. 

#### <a name="docker-for-linux-containers"></a>Platformy docker dla kontenerów systemu Linux

Jeśli używasz Windows do tworzenia i testowania kontenery dla urządzeń z systemem Linux, możesz użyć [Docker for Windows](https://www.docker.com/docker-windows) jako aparat kontenera. Platformy docker można skonfigurować w celu [korzystanie z kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Należy zainstalować platformę Docker i skonfigurować go przed zainstalowaniem usługi IoT Edge. Kontenery systemu Linux nie są obsługiwane na urządzeniach Windows w środowisku produkcyjnym. 

Jeśli urządzenia usługi IoT Edge jest komputerem Windows, sprawdź, czy spełnia [wymagania systemowe](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) funkcji Hyper-v.

## <a name="install-iot-edge-on-a-new-device"></a>Zainstaluj usługi IoT Edge na nowe urządzenie

>[!NOTE]
>Pakiety oprogramowania w usłudze Azure IoT Edge to z zastrzeżeniem postanowień licencyjnych, znajduje się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne, aby korzystać z pakietu. Twoja instalacja i używanie pakietu stanowi zaakceptowania przez korzystającego tych warunków. Jeśli nie zgadzasz się z warunkami licencji, nie należy używać pakietu.

Skrypt programu PowerShell pobiera i instaluje demona zabezpieczeń usługi Azure IoT Edge. Demon zabezpieczeń następnie uruchamia pierwsze dwa moduły środowiska uruchomieniowego, agent usługi IoT Edge, który umożliwia używanie wdrożeń zdalnego innych modułów. 

Po zainstalowaniu środowiska uruchomieniowego usługi IoT Edge po raz pierwszy na urządzeniu, musisz aprowizować urządzenia przy użyciu tożsamości z usługi IoT hub. Pojedyncze urządzenie usługi IoT Edge mogą być udostępniane ręcznie przy użyciu parametrów połączenia urządzenia dostarczane przez usługę IoT Hub. Alternatywnie można użyć usługi Device Provisioning Service, aby automatycznie aprowizować urządzenia, co jest przydatne, jeśli masz wiele urządzeń, aby skonfigurować. W zależności od wybranego inicjowania obsługi administracyjnej wybierz skrypt instalacyjny odpowiedniego. 

Poniższe sekcje opisują typowe przypadki użycia i Parametry skryptu instalacji usługi IoT Edge na nowym urządzeniu. 

### <a name="option-1-install-and-manually-provision"></a>Opcja 1: Instalacja oraz ręcznie zainicjować obsługę administracyjną

W tej pierwszej opcji możesz podać parametry połączenia urządzenia generowane przez usługę IoT Hub, aprowizować urządzenie. 

Postępuj zgodnie z instrukcjami w [zarejestrować nowe urządzenie usługi Azure IoT Edge](how-to-register-device-portal.md) zarejestrować urządzenie i pobieranie parametrów połączenia urządzenia. 

Ten przykład przedstawia ręcznej instalacji przy użyciu kontenerów Windows:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

Po zainstalowaniu i ręcznie aprowizowanie urządzenia można użyć dodatkowych parametrów do modyfikowania instalacji, w tym:
* Bezpośrednie kierowanie ruchu za pośrednictwem serwera proxy
* Wskaż Instalator w trybie offline katalogu
* Deklarowanie obraz kontenera określonego agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym
* Pomiń instalację Moby interfejsu wiersza polecenia

Aby uzyskać więcej informacji na temat tych opcji instalacji, przeczytaj ten artykuł lub pominąć, aby dowiedzieć się więcej na temat [wszystkie parametry instalacji](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalowanie i automatycznie aprowizować

Ta druga opcja służy do aprowizacji urządzenia przy użyciu IoT Hub Device Provisioning Service. Podaj **identyfikator zakresu** z wystąpienia usługi Device Provisioning i **identyfikator rejestracji** na urządzeniu.

Postępuj zgodnie z instrukcjami w [tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na Windows](how-to-auto-provision-simulated-device-windows.md) konfiguracji usługi Device Provisioning i pobrać jego **identyfikator zakresu**symulowane urządzenie TPM i pobrać jego  **Identyfikator rejestracji**, następnie utworzyć rejestrację indywidualną. Gdy urządzenie jest zarejestrowane w usłudze IoT Hub, kontynuuj instalację.  

   >[!TIP]
   >Zachowaj okna, które działa symulatora modułu TPM, otwórz podczas instalacji programu i testowania. 

Instalacja automatyczna za pomocą kontenerów Windows można znaleźć w poniższym przykładzie:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

Po zainstalowaniu i ręcznie aprowizowanie urządzenia można użyć dodatkowych parametrów do modyfikowania instalacji, w tym:
* Bezpośrednie kierowanie ruchu za pośrednictwem serwera proxy
* Wskaż Instalator w trybie offline katalogu
* Deklarowanie obraz kontenera określonego agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym
* Pomiń instalację Moby interfejsu wiersza polecenia

Aby uzyskać więcej informacji na temat tych opcji instalacji, przeczytaj ten artykuł lub pominąć, aby dowiedzieć się więcej na temat [wszystkie parametry instalacji](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Aktualizowanie istniejącej instalacji

Jeśli zostały już zainstalowane środowisko uruchomieniowe usługi IoT Edge na urządzeniu zanim i zaaprowizowane przy użyciu tożsamości z usługi IoT Hub, można użyć skryptu uproszczonej instalacji. Flaga `-ExistingConfig` deklaruje, że plik konfiguracji usługi IoT Edge jest już na urządzeniu. Plik konfiguracji zawiera informacje o ustawieniach urządzenia tożsamość, a także certyfikatów i sieci. Można użyć tej opcji instalacji, czy pierwotnie zaaprowizowano urządzenie, ręcznie lub automatycznie. 

Aby uzyskać więcej informacji, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).

W tym przykładzie przedstawiono instalację, który wskazuje istniejący plik konfiguracyjny i używa kontenerów Windows: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

Po zainstalowaniu usługi IoT Edge z istniejącego pliku konfiguracji, można użyć dodatkowych parametrów do modyfikowania instalacji, w tym:
* Bezpośrednie kierowanie ruchu za pośrednictwem serwera proxy
* Wskaż Instalator w trybie offline katalogu lub 
* Pomiń instalację Moby interfejsu wiersza polecenia. 

Nie można zadeklarować obrazu kontenera agenta usługi IoT Edge Parametry skryptu, ponieważ te informacje jest już ustawiona w pliku konfiguracji z poprzedniej instalacji. Jeśli chcesz zmodyfikować agenta obrazu kontenera, to zrobić w pliku config.yaml. 

Aby uzyskać więcej informacji na temat tych opcji instalacji, przeczytaj ten artykuł lub pominąć, aby dowiedzieć się więcej na temat [wszystkie parametry instalacji](#all-installation-parameters).

## <a name="offline-installation"></a>Instalacja w trybie offline

Podczas instalacji są pobierane cztery pliki: 
* Zip demona (iotedgd) zabezpieczeń usługi IoT Edge 
* Moby aparat zip
* Zip Moby interfejsu wiersza polecenia
* Visual C++ pakiet redystrybucyjny (środowisko uruchomieniowe VC) msi

Możesz pobrać jeden lub wszystkie z tych plików, które wcześniej na urządzeniu, a następnie punktu skrypt instalacji w katalogu, który zawiera pliki. Instalator sprawdza, czy katalog najpierw, a następnie pobiera tylko składniki, które nie zostaną znalezione. Jeśli wszystkie cztery pliki są dostępne w trybie offline, można zainstalować bez połączenia internetowego. Ta funkcja umożliwia również zastąpienie online wersji co najmniej jednego składnika.  

Aby uzyskać najnowsze pliki instalacyjne wraz z poprzednich wersji, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)

Aby zainstalować przy użyciu składników w trybie offline, należy użyć `-OfflineInstallationPath` parametru i podaj ścieżkę bezwzględną do katalogu plików. Na przykład:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Wszystkie parametry instalacji

Przedstawione w poprzednich sekcjach wprowadzono typowe scenariusze instalacji za pomocą przykładów dotyczących sposobów używania parametrów w celu zmodyfikowania skryptu instalacji. Ta sekcja zawiera odwołanie do tabeli prawidłowych parametrów, których można użyć do zainstalowania usługi IoT Edge. Aby uzyskać więcej informacji, uruchom `get-help Install-SecurityDaemon -full` w oknie programu PowerShell. 

Aby zainstalować usługi IoT Edge przy użyciu istniejącej konfiguracji, polecenia instalacji można użyć tych wspólnych parametrów: 

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Ręcznie** | Brak | **Przełącz parametru**. Każda instalacja musi być zadeklarowany ręcznie, punkty dystrybucji lub existingconfig.<br><br>Deklaruje zapewni parametry połączenia urządzenia, aby aprowizować urządzenie ręcznie |
| **Usługa DPS** | Brak | **Przełącz parametru**. Każda instalacja musi być zadeklarowany ręcznie, punkty dystrybucji lub existingconfig.<br><br>Deklaruje zapewni urządzenia inicjowania obsługi usługi (DPS) identyfikator zakresu i identyfikator rejestracji urządzenia do aprowizacji za pośrednictwem usługi DPS.  |
| **ExistingConfig** | Brak | **Przełącz parametru**. Każda instalacja musi być zadeklarowany ręcznie, punkty dystrybucji lub existingconfig.<br><br>Deklaruje, że plik config.yaml już istnieje na urządzeniu z jego informacje o udostępnianiu. |
| **DeviceConnectionString** | Parametry połączenia, z urządzenia usługi IoT Edge zarejestrowane w usłudze IoT Hub, w pojedynczym cudzysłowie | **Wymagane** dla instalacji ręcznej. Jeśli nie zostaną podane parametry połączenia w parametrach skrypt, użytkownik jest monitowany jednego podczas instalacji. |
| **ScopeId** | Identyfikator zakresu z wystąpienia usługi Device Provisioning skojarzonych z Twoim Centrum IoT Hub. | **Wymagane** instalacji punktu dystrybucji. Jeśli nie podasz Identyfikatora zakresu, w polu Parametry skryptu, użytkownik jest monitowany jednego podczas instalacji. |
| **RegistrationId** | Identyfikator rejestracji, generowane przez urządzenie | **Wymagane** instalacji punktu dystrybucji. Jeśli nie podasz identyfikator rejestracji, za pomocą parametrów skryptu, użytkownik jest monitowany jednego podczas instalacji. |
| **ContainerOs** | **Windows** lub **systemu Linux** | Jeśli brak kontenera jest określana przez system operacyjny Linux jest wartością domyślną. Dla kontenerów Windows aparatu kontenera będzie dostępny w instalacji. Dla kontenerów systemu Linux musisz zainstalować aparat container przed rozpoczęciem instalacji. Uruchamianie kontenerów systemu Linux na Windows jest przydatny rozwojowych, ale nie są obsługiwane w środowisku produkcyjnym. |
| **Proxy** | Adres URL serwera proxy | Ten parametr należy uwzględnić, jeśli urządzenie musi przechodzić przez serwer proxy do uzyskiwania dostępu do Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tablica skrótów parametrów i wartości | Podczas instalacji są wprowadzane kilka żądań sieci web. Użyj tego pola, aby ustawić parametry te żądania sieci web. Ten parametr jest przydatne skonfigurować poświadczenia dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr jest dołączony, program instalacyjny sprawdzi katalog iotedged zip, Moby aparatu zip, zip Moby interfejsu wiersza polecenia i plików MSI środowiska uruchomieniowego VC wymaganych do instalacji. Jeśli wszystkie cztery pliki znajdują się w katalogu, możesz zainstalować usługi IoT Edge podczas w trybie offline. Ten parametr umożliwia również zastąpienie wersji online określonego składnika. |
| **AgentImage** | Identyfikator URI obrazu agenta usługi IoT Edge | Domyślnie nowa instalacja usługi IoT Edge taga najnowsza wersja stopniowe obraz agenta usługi IoT Edge. Użyj tego parametru można ustawić określony tag wersji obrazu lub w celu udostępnienia swój własny obraz agenta. Aby uzyskać więcej informacji, zobacz [tagi usługi IoT Edge zrozumieć](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwa użytkownika** | Nazwa użytkownika rejestru kontenerów | Użyj tego parametru, tylko wtedy, gdy parametr - AgentImage jest ustawiony do kontenera w rejestrze prywatnym. Należy podać nazwę użytkownika z dostępem do rejestru. |
| **Hasło** | Ciąg bezpieczne hasło | Użyj tego parametru, tylko wtedy, gdy parametr - AgentImage jest ustawiony do kontenera w rejestrze prywatnym. Podaj hasło dostępu do rejestru. | 
| **SkipMobyCli** | Brak | Dotyczy tylko jeśli - ContainerOS jest równa Windows. Nie należy instalować na $MobyInstallDirectory Moby interfejsu wiersza polecenia (docker.exe). |

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

Po zainstalowaniu nowego tylko moduł powinien zostać wyświetlony jest uruchomiona **edgeAgent**. Po zakończeniu [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md), widoczne będą inne. 

## <a name="manage-module-containers"></a>Zarządzanie kontenerami modułu

Usługa IoT Edge wymaga aparatu kontenera, na urządzeniu z. Podczas wdrażania modułu na urządzeniu, środowisko uruchomieniowe usługi IoT Edge używa aparatu kontenera do ściągania obrazu kontenera z rejestru w chmurze. Usługa IoT Edge umożliwia interakcję z moduły, a następnie pobrać dzienniki, ale czasami możesz chcieć użyć aparatu kontenera do interakcji z samego kontenera. 

Aby uzyskać więcej informacji o pojęciach dotyczących modułu, zobacz [modułów Omówienie usługi Azure IoT Edge](iot-edge-modules.md). 

Jeśli korzystasz z kontenerów Windows na urządzeniu z systemem Windows IoT Edge, instalacji usługi IoT Edge uwzględnione Moby aparatu kontenera. Jeżeli projektujesz kontenerów systemu Linux na maszynie deweloperskiej Windows, prawdopodobnie używasz pulpitu platformy Docker. Aparat Moby była oparta na te same standardy, jak Docker i zaprojektowano tak, aby uruchomić równolegle na tym samym komputerze co pulpitu platformy Docker. Z tego powodu aby kontenery docelowej zarządzana przez aparat Moby trzeba specjalnie docelowe silnika zamiast platformy Docker. 

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

## <a name="uninstall-iot-edge"></a>Odinstalowywanie usługi IoT Edge

Jeśli chcesz usunąć instalację usługi IoT Edge na urządzeniu Windows, użyj następującego polecenia z administracyjne okna programu PowerShell. To polecenie usuwa środowisko uruchomieniowe usługi IoT Edge wraz z istniejącej konfiguracji i danych aparatu Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Jeśli planujesz ponownie zainstalować usługi IoT Edge na urządzeniu, opuszczenie `-DeleteConfig` i `-DeleteMobyDataRoot` parametry, aby ponownie zainstalować demona zabezpieczeń później przy użyciu istniejących informacji o konfiguracji. 

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy z instalacją usługi IoT Edge prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).
