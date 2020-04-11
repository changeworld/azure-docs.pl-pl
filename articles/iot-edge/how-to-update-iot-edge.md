---
title: Aktualizowanie wersji usługi IoT Edge na urządzeniach — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Jak zaktualizować urządzenia Usługi IoT Edge w celu uruchomienia najnowszych wersji demona zabezpieczeń i środowiska wykonawczego usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113301"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizowanie demona zabezpieczeń i środowiska uruchomieniowego usługi IoT Edge

W miarę jak usługa IoT Edge wydaje nowe wersje, należy zaktualizować urządzenia usługi IoT Edge w celu uzyskania najnowszych funkcji i ulepszeń zabezpieczeń. Ten artykuł zawiera informacje dotyczące aktualizowania urządzeń usługi IoT Edge, gdy dostępna jest nowa wersja.

Dwa składniki urządzenia IoT Edge muszą zostać zaktualizowane, jeśli chcesz przejść do nowszej wersji. Pierwszym z nich jest demon zabezpieczeń, który działa na urządzeniu i uruchamia moduły środowiska wykonawczego po uruchomieniu urządzenia. Obecnie demon zabezpieczeń można zaktualizować tylko z samego urządzenia. Drugi składnik to środowisko wykonawcze składające się z modułów agenta usługi IoT Edge i usługi IoT Edge. W zależności od sposobu struktury wdrożenia środowisko wykonawcze można zaktualizować z urządzenia lub zdalnie.

Aby znaleźć najnowszą wersję usługi Azure IoT Edge, zobacz [wersje usługi Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="update-the-security-daemon"></a>Aktualizowanie demona zabezpieczeń

Demon zabezpieczeń usługi IoT Edge jest składnikiem macierzystym, który należy zaktualizować za pomocą menedżera pakietów na urządzeniu Usługi IoT Edge.

Sprawdź wersję demona zabezpieczeń działającego na urządzeniu za `iotedge version`pomocą polecenia .

### <a name="linux-devices"></a>Urządzenia z systemem Linux

Na urządzeniach z systemem Linux x64 użyj apt-get lub odpowiedniego menedżera pakietów, aby zaktualizować demona zabezpieczeń do najnowszej wersji.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Jeśli chcesz zaktualizować do określonej wersji demona zabezpieczeń, znajdź wersję, na którą chcesz kierować dane z [wersji usługi IoT Edge](https://github.com/Azure/azure-iotedge/releases). W tej wersji znajdź odpowiednie pliki **libiothsm-std** i **iotedge** dla swojego urządzenia. Dla każdego pliku kliknij prawym przyciskiem myszy łącze do pliku i skopiuj adres łącza. Użyj adresu łącza, aby zainstalować określone wersje tych składników:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Urządzenia z systemem Windows

Na urządzeniach z systemem Windows użyj skryptu programu PowerShell, aby zaktualizować demona zabezpieczeń. Skrypt automatycznie pobiera najnowszą wersję demona zabezpieczeń.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Uruchomienie polecenia Update-IoTEdge usuwa i aktualizuje demona zabezpieczeń z urządzenia wraz z dwoma obrazami kontenerów środowiska wykonawczego. Plik config.yaml jest przechowywany na urządzeniu, a także dane z aparatu kontenerów Moby (jeśli używasz kontenerów systemu Windows). Zachowanie informacji o konfiguracji oznacza, że nie trzeba ponownie podawać parametry połączenia lub informacje o usłudze aprowizacji urządzeń dla urządzenia podczas procesu aktualizacji.

Jeśli chcesz zaktualizować do określonej wersji demona zabezpieczeń, znajdź wersję, na którą chcesz kierować dane z [wersji usługi IoT Edge](https://github.com/Azure/azure-iotedge/releases). W tej wersji pobierz plik **Microsoft-Azure-IoTEdge.cab.** Następnie użyj `-OfflineInstallationPath` parametru, aby wskazać lokalizację pliku lokalnego. Przykład:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Parametr `-OfflineInstallationPath` wyszukuje plik o nazwie **Microsoft-Azure-IoTEdge.cab** w dostarczonym katalogu. Począwszy od wersji IoT Edge 1.0.9-rc4, dostępne są dwa pliki .cab, jeden dla urządzeń AMD64 i jeden dla ARM32. Pobierz poprawny plik dla urządzenia, a następnie zmień jego nazwę, aby usunąć sufiks architektury.

Aby uzyskać więcej informacji na `Get-Help Update-IoTEdge -full` temat opcji aktualizacji, użyj polecenia lub zapoznaj się [ze wszystkimi parametrami instalacji](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Aktualizowanie kontenerów środowiska wykonawczego

Sposób aktualizowania agenta usługi IoT Edge i kontenerów centrum usługi IoT Edge zależy od tego, czy używasz tagów stopniowych (takich jak 1.0) czy określonych tagów (takich jak 1.0.7) we wdrożeniu.

Sprawdź wersję agenta usługi IoT Edge i modułów koncentratora usługi IoT Edge aktualnie na urządzeniu za pomocą poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub`.

  ![Znajdowanie wersji kontenera w dziennikach](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Opis tagów IoT Edge

Obrazy agenta usługi IoT Edge i centrum usługi IoT Edge są oznaczone wersją usługi IoT Edge, z którą są skojarzone. Istnieją dwa różne sposoby używania tagów z obrazami środowiska wykonawczego:

* **Tagi toczne** — użyj tylko dwóch pierwszych wartości numeru wersji, aby uzyskać najnowszy obraz, który pasuje do tych cyfr. Na przykład 1.0 jest aktualizowana za każdym razem, gdy istnieje nowa wersja, aby wskazać najnowszą wersję 1.0.x. Jeśli środowisko wykonawcze kontenera na urządzeniu Usługi IoT Edge ponownie ściąga obraz, moduły środowiska wykonawczego są aktualizowane do najnowszej wersji. Takie podejście jest zalecane do celów rozwojowych. Wdrożenia z witryny Azure portal domyślnie do tagów rolowania.

* **Określone tagi** — użyj wszystkich trzech wartości numeru wersji, aby jawnie ustawić wersję obrazu. Na przykład 1.0.7 nie zmieni się po jego początkowej wersji. Można zadeklarować nowy numer wersji w manifeście wdrożenia, gdy jesteś gotowy do aktualizacji. Takie podejście jest zalecane do celów produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Aktualizowanie obrazu znacznika stopniowego

Jeśli używasz tagów toczenia we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0),** musisz wymusić środowisko wykonawcze kontenera na urządzeniu, aby wyciągnąć najnowszą wersję obrazu.

Usuń lokalną wersję obrazu z urządzenia Usługi IoT Edge. Na komputerach z systemem Windows odinstalowanie demona zabezpieczeń powoduje również usunięcie obrazów środowiska wykonawczego, więc nie trzeba ponownie wykonać tego kroku.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Może być konieczne użycie `-f` flagi siły, aby usunąć obrazy.

Usługa IoT Edge pobiera najnowsze wersje obrazów środowiska wykonawczego i automatycznie uruchamia je ponownie na urządzeniu.

### <a name="update-a-specific-tag-image"></a>Aktualizowanie określonego obrazu znacznika

Jeśli używasz określonych tagów we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.8),** wystarczy zaktualizować tag w manifeście wdrożenia i zastosować zmiany na urządzeniu.

1. W Centrum IoT w portalu Azure wybierz urządzenie usługi IoT Edge i wybierz pozycję **Ustaw moduły.**

1. W sekcji **Moduły krawędzi IoT** wybierz pozycję **Ustawienia środowiska uruchomieniowego**.

   ![Konfigurowanie ustawień środowiska uruchomieniowego](./media/how-to-update-iot-edge/configure-runtime.png)

1. W **obszarze Ustawienia środowiska uruchomieniego**zaktualizuj wartość **Obrazu** dla **centrum krawędzi** za pomocą żądanej wersji. Nie wybieraj jeszcze opcji **Zapisz.**

   ![Aktualizacja wersji obrazu centrum krawędzi](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Zwiń ustawienia **koncentratora krawędzi** lub przewiń w dół i zaktualizuj wartość **Obrazu** dla **agenta krawędzi** w tej samej żądanej wersji.

   ![Aktualizacja wersji programu Edge Hub Agent](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Wybierz **pozycję Zapisz**.

1. Wybierz **pozycję Przejrzyj + utwórz,** przejrzyj wdrożenie i wybierz pozycję **Utwórz**.

## <a name="update-offline-or-to-a-specific-version"></a>Aktualizowanie w trybie offline lub do określonej wersji

Jeśli chcesz zaktualizować urządzenie w trybie offline lub zaktualizować do określonej wersji usługi IoT Edge, a nie do najnowszej wersji, możesz to zrobić za pomocą parametru. `-OfflineInstallationPath`

Dwa składniki są używane do aktualizacji urządzenia Usługi IoT Edge:

* Skrypt programu PowerShell zawierający instrukcje instalacji
* Kabina Usługi Microsoft Azure IoT Edge zawierająca demon zabezpieczeń usługi IoT Edge (iotedged), silnik kontenerów Moby i moby CLI

1. Aby zapoznać się z najnowszymi plikami instalacyjnymi usługi IoT Edge wraz z poprzednimi wersjami, zobacz [Wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Znajdź wersję, którą chcesz zainstalować, i pobierz następujące pliki z sekcji **Zasoby** informacji o wersji na urządzenie IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z wersji 1.0.9 lub nowszych lub Microsoft-Azure-IoTEdge.cab z wersji 1.0.8 i starszych.

   Microsoft-Azure-IotEdge-arm32.cab jest również dostępny od 1.0.9 tylko do celów testowych. Usługa IoT Edge nie jest obecnie obsługiwana na urządzeniach z systemem Windows ARM32.

   Ważne jest, aby używać skryptu programu PowerShell z tej samej wersji co plik cab, którego używasz, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

3. Jeśli pobrany plik cab zawiera sufiks architektury, zmień nazwę pliku na **tylko Microsoft-Azure-IoTEdge.cab**.

4. Aby zaktualizować ze składnikami w trybie offline, [kropka źródła](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) lokalnej kopii skryptu programu PowerShell. Następnie użyj `-OfflineInstallationPath` parametru jako `Update-IoTEdge` części polecenia i podaj ścieżkę bezwzględną do katalogu plików. Na przykład:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Aktualizacja do wersji kandydackiej

Usługa Azure IoT Edge regularnie wydaje nowe wersje usługi Usługi IoT Edge. Przed każdym stabilne wydanie, istnieje co najmniej jedna wersja kandydata wydania (RC). Wersje RC zawierają wszystkie planowane funkcje wydania, ale nadal przechodzą testy i walidację. Jeśli chcesz przetestować nową funkcję wcześniej, możesz zainstalować wersję RC i przekazać opinię za pośrednictwem gitHub.

Wersje kandydujące wersji zgodnie z tą samą konwencją numeracji wydań, ale mają **-rc** plus przyrostowy numer dołączany do końca. Możesz zobaczyć kandydatów do wydania na tej samej liście [wersji usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) jako wersje stabilne. Na przykład znajdź **1.0.7-rc1** i **1.0.7-rc2**, dwóch kandydatów do wydania, które pojawiły się przed **1.0.7**. Można również zobaczyć, że wersje RC są oznaczone etykietami **wersji wstępnej.**

Moduły agenta i koncentratora usługi IoT Edge mają wersje RC, które są oznaczone tą samą konwencją. Na przykład **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

W wersji zapoznawczej wersje kandydatów do wydania nie są uwzględniane jako najnowsza wersja docelowa dla zwykłych instalatorów. Zamiast tego należy ręcznie kierować zasoby dla wersji RC, które chcesz przetestować. W przeważającej części instalowanie lub aktualizowanie wersji RC jest takie samo jak kierowanie na inną konkretną wersję aplikacji IoT Edge.

Skorzystaj z sekcji w tym artykule, aby dowiedzieć się, jak zaktualizować urządzenie usługi IoT Edge do określonej wersji demona zabezpieczeń lub modułów środowiska uruchomieniowego.

Jeśli instalujesz usługę IoT Edge na nowym komputerze, skorzystaj z następujących łączy, aby dowiedzieć się, jak zainstalować określoną wersję w zależności od systemu operacyjnego urządzenia:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Następne kroki

Wyświetlanie najnowszych [wersji usługi Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniami na [blogu Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/)
