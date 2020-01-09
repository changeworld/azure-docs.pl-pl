---
title: Aktualizacja wersji usługi IoT Edge na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak zaktualizować urządzeń usługi IoT Edge, aby uruchamiać najnowsze wersje demona zabezpieczeń i środowisko uruchomieniowe usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e29420b8e029a7e1723f969753eb2081381a8e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434156"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska uruchomieniowego

Ponieważ usługa IoT Edge zwalnia nowe wersje, należy zaktualizować urządzenia IoT Edge pod kątem najnowszych funkcji i ulepszeń zabezpieczeń. Ten artykuł zawiera informacje o sposobie aktualizowania urządzenia usługi IoT Edge, gdy dostępna jest nowa wersja.

Dwa składniki urządzenia usługi IoT Edge należy zaktualizować, jeśli chcesz przejść do nowszej wersji. Pierwszy to demon zabezpieczeń, który działa na urządzeniu i uruchamia moduły środowiska uruchomieniowego, gdy urządzenie zostanie uruchomione. Obecnie usługa demona zabezpieczeń można aktualizować tylko z samego urządzenia. Drugi składnik to środowisko uruchomieniowe składające się z centrów IoT Edge i IoT Edge agentów. W zależności od strukturą wdrożenia środowisko uruchomieniowe może zostać zaktualizowana z urządzenia lub zdalnie.

Aby uzyskać najnowszą wersję usługi Azure IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizacja demona zabezpieczeń

Demon zabezpieczeń usługi IoT Edge jest natywny składnik, który musi zostać zaktualizowana przy użyciu Menedżera pakietów na urządzeniu usługi IoT Edge.

Zapoznaj się z wersją demona zabezpieczeń na urządzeniu z za pomocą polecenia `iotedge version`.

### <a name="linux-devices"></a>Urządzeniami z systemem Linux

Na urządzeniach z systemem Linux x64 Użyj apt-get lub odpowiedniego Menedżera pakietów, aby zaktualizować demona zabezpieczeń.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Na urządzeniach z systemem Linux ARM32 wykonaj kroki opisane w temacie [Install Azure IoT Edge Runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) , aby zainstalować najnowszą wersję demona zabezpieczeń.

### <a name="windows-devices"></a>Urządzenia Windows

Na urządzeniach z systemem Windows należy zaktualizować demona zabezpieczeń za pomocą skryptu programu PowerShell. Skrypt automatycznie pobiera najnowszą wersję demona zabezpieczeń.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Uruchomienie polecenia Update-IoTEdge powoduje usunięcie i zaktualizowanie demona zabezpieczeń z urządzenia wraz z dwoma obrazami kontenera środowiska uruchomieniowego. Plik config. YAML jest przechowywany na urządzeniu, a także dane z aparatu kontenera Moby (Jeśli używasz kontenerów systemu Windows). Przechowywanie informacji o konfiguracji oznacza, że nie trzeba podawać parametrów połączenia ani informacji o usłudze Device Provisioning dla urządzenia ponownie w trakcie procesu aktualizacji.

Jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, Pobierz odpowiedni plik Microsoft-Azure-IoTEdge. cab z [wersji IoT Edge](https://github.com/Azure/azure-iotedge/releases). Następnie użyj parametru `-OfflineInstallationPath`, aby wskazać lokalizację pliku. Aby uzyskać więcej informacji, zobacz [Instalacja w trybie offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aktualizuj kontenerów środowiska wykonawczego

Sposób aktualizowania kontenerów IoT Edge i kontenera Centrum IoT Edge zależy od tego, czy używane są Tagi walcowe (na przykład 1,0) czy konkretne Tagi (takie jak 1.0.7).

Sprawdź wersję agenta IoT Edge i IoT Edge modułów centrów dostępnych obecnie na urządzeniu przy użyciu poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub`.

  ![Znajdź wersję kontenera w dziennikach](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Omówienie usługi IoT Edge tagów

Obraz IoT Edge i centrum IoT Edge są oznaczone za pomocą IoT Edge wersji, z którą są skojarzone. Istnieją dwa różne sposoby przy użyciu obrazów środowiska uruchomieniowego za pomocą tagów:

* **Stopniowe tagi** — Użyj tylko dwie pierwsze wartości numer wersji można pobrać najnowszego obrazu, który odpowiada te cyfry. Na przykład 1.0 jest aktualizowany zawsze wtedy, gdy istnieje nowa wersja, aby wskazywał najnowszą wersję 1.0.x. Jeśli kontener środowiska uruchomieniowego na urządzeniu usługi IoT Edge ściąga obraz ponownie, moduły środowiska uruchomieniowego są aktualizowane do najnowszej wersji. To podejście jest zalecane do celów programistycznych. Wdrożenia z domyślnego portalu Azure do stopniowego tagów.

* **Określone znaczniki** — Użyj wszystkich trzech wartości numer wersji można jawnie ustawić wersję obrazu. Na przykład 1.0.7 nie zmieni się po początkowej wersji. Gdy wszystko będzie gotowe do aktualizacji, można zadeklarować nowego numeru wersji w pliku manifestu wdrożenia. To podejście jest zalecane do celów produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Uaktualnianie stopniowe obrazu znacznika

Jeśli używasz stopniowe tagów w danym wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0**), a następnie należy wymusić kontener środowiska uruchomieniowego na urządzeniu, aby ściągnąć najnowszą wersję obrazu.

Usuń lokalną wersję obrazu z urządzenia usługi IoT Edge. Na maszynach z systemem Windows Dezinstalacja demona zabezpieczeń usuwa również obrazy środowiska uruchomieniowego, więc nie trzeba ponownie wykonać tego kroku.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Może być konieczne użycie uruchomionego z wymuszeniem `-f` flagę, aby usunąć obrazy.

Usługa IoT Edge pobierania najnowszych wersji obrazów środowiska uruchomieniowego i automatyczne uruchamianie ich na swoim urządzeniu ponownie.

### <a name="update-a-specific-tag-image"></a>Aktualizuj obraz konkretny tag

Jeśli używasz określonych tagów we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.8**), wystarczy zaktualizować tag w manifeście wdrożenia i zastosować zmiany na urządzeniu.

1. W IoT Hub w Azure Portal wybierz urządzenie IoT Edge, a następnie wybierz pozycję **Ustaw moduły**.

1. W sekcji **IoT Edge modułów** wybierz pozycję **Ustawienia środowiska uruchomieniowego**.

   ![Konfigurowanie ustawień środowiska uruchomieniowego](./media/how-to-update-iot-edge/configure-runtime.png)

1. W obszarze **Ustawienia środowiska uruchomieniowego**zaktualizuj wartość **obrazu** dla **centrum brzegowego** z odpowiednią wersją. Nie zaznaczaj jeszcze opcji **Zapisz** .

   ![Aktualizuj wersję obrazu centrum brzegowego](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Zwiń ustawienia **centrum Edge** lub przewiń w dół i zaktualizuj wartość **obrazu** dla **agenta usługi Edge** z tą samą odpowiednią wersją.

   ![Aktualizuj wersję agenta centrum brzegowego](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj wdrożenie i wybierz pozycję **Utwórz**.

## <a name="update-to-a-release-candidate-version"></a>Aktualizacja do wersji Release Candidate

Azure IoT Edge regularnie zwalnia nowe wersje usługi IoT Edge. Przed każdą stabilną wersją jest dostępna co najmniej jedna wersja Release Candidate (RC). Wersje RC obejmują wszystkie planowane funkcje wydania, ale nadal przechodzą przez procesy testowania i sprawdzania poprawności wymagane do stabilnej wersji. Jeśli chcesz przetestować nową funkcję wcześniej, możesz zainstalować wersję RC i przekazać opinię za pomocą usługi GitHub.

Wersje Release Candidate są zgodne z tą samą konwencją numerowania wydań, ale mają **-RC** Plus numer przyrostowy dołączony do końca. Kandydatów wersji można zobaczyć na tej samej liście wersji [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) , co w przypadku stabilnych wersji. Na przykład Znajdź **1.0.7-RC1** i **1.0.7-RC2**, dwie wersje kandydatów, które zostały dostarczone przed **1.0.7**. Możesz również sprawdzić, czy wersje RC są oznaczone etykietami **wersji wstępnej** .

W ramach wersji zapoznawczych wersje Release Candidate nie są uwzględniane w najnowszej wersji docelowej zwykłych instalatorów. Zamiast tego należy ręcznie określić zasoby dla wersji RC, która ma zostać przetestowana. W zależności od systemu operacyjnego urządzenia IoT Edge należy skorzystać z poniższych sekcji w celu zaktualizowania IoT Edge do określonej wersji:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Następne kroki

Wyświetl najnowsze [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniemi w [blogu Internet rzeczy](https://azure.microsoft.com/blog/topics/internet-of-things/)