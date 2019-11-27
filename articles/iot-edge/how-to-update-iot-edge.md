---
title: Aktualizacja wersji usługi IoT Edge na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak zaktualizować urządzeń usługi IoT Edge, aby uruchamiać najnowsze wersje demona zabezpieczeń i środowisko uruchomieniowe usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456550"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska uruchomieniowego

Ponieważ usługa IoT Edge zwalnia nowe wersje, należy zaktualizować urządzenia IoT Edge pod kątem najnowszych funkcji i ulepszeń zabezpieczeń. Ten artykuł zawiera informacje o sposobie aktualizowania urządzenia usługi IoT Edge, gdy dostępna jest nowa wersja. 

Dwa składniki urządzenia usługi IoT Edge należy zaktualizować, jeśli chcesz przejść do nowszej wersji. Pierwszy to demon zabezpieczeń, który działa na urządzeniu i uruchamia moduły środowiska uruchomieniowego, gdy urządzenie zostanie uruchomione. Obecnie usługa demona zabezpieczeń można aktualizować tylko z samego urządzenia. Drugi składnik to środowisko uruchomieniowe składające się z centrów IoT Edge i IoT Edge agentów. W zależności od strukturą wdrożenia środowisko uruchomieniowe może zostać zaktualizowana z urządzenia lub zdalnie. 

Aby znaleźć najnowszą wersję Azure IoT Edge, zobacz [wersje Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizacja demona zabezpieczeń

Demon zabezpieczeń usługi IoT Edge jest natywny składnik, który musi zostać zaktualizowana przy użyciu Menedżera pakietów na urządzeniu usługi IoT Edge. 

Sprawdź wersję demona zabezpieczeń działającą na urządzeniu za pomocą polecenia `iotedge version`. 

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

Uruchomienie polecenia Update-IoTEdge powoduje usunięcie demona zabezpieczeń z urządzenia wraz z dwoma obrazami kontenera środowiska uruchomieniowego. Plik config. YAML jest przechowywany na urządzeniu, a także dane z aparatu kontenera Moby (Jeśli używasz kontenerów systemu Windows). Przechowywanie informacji o konfiguracji oznacza, że nie trzeba podawać parametrów połączenia ani informacji o usłudze Device Provisioning dla urządzenia ponownie w trakcie procesu aktualizacji. 

Jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, Pobierz odpowiedni plik Microsoft-Azure-IoTEdge. cab z [wersji IoT Edge](https://github.com/Azure/azure-iotedge/releases). Następnie użyj parametru `-OfflineInstallationPath`, aby wskazać lokalizację pliku. Aby uzyskać więcej informacji, zobacz [Instalacja w trybie offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aktualizuj kontenerów środowiska wykonawczego

Sposób aktualizowania kontenerów IoT Edge i kontenera Centrum IoT Edge zależy od tego, czy używane są Tagi walcowe (na przykład 1,0) czy konkretne Tagi (takie jak 1.0.7). 

Sprawdź wersję agenta IoT Edge i IoT Edge modułów centrów dostępnych obecnie na urządzeniu przy użyciu poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub`. 

  ![Znajdź wersję kontenera w dziennikach](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Omówienie usługi IoT Edge tagów

Obraz IoT Edge i centrum IoT Edge są oznaczone za pomocą IoT Edge wersji, z którą są skojarzone. Istnieją dwa różne sposoby przy użyciu obrazów środowiska uruchomieniowego za pomocą tagów: 

* **Tagi walcowe** — Użyj tylko dwóch pierwszych wartości numeru wersji, aby uzyskać najnowszy obraz, który jest zgodny z tymi cyframi. Na przykład 1.0 jest aktualizowany zawsze wtedy, gdy istnieje nowa wersja, aby wskazywał najnowszą wersję 1.0.x. Jeśli kontener środowiska uruchomieniowego na urządzeniu usługi IoT Edge ściąga obraz ponownie, moduły środowiska uruchomieniowego są aktualizowane do najnowszej wersji. To podejście jest zalecane do celów programistycznych. Wdrożenia z domyślnego portalu Azure do stopniowego tagów. 
* **Określone Tagi** — Użyj wszystkich trzech wartości numeru wersji, aby jawnie ustawić wersję obrazu. Na przykład 1.0.7 nie zmieni się po początkowej wersji. Gdy wszystko będzie gotowe do aktualizacji, można zadeklarować nowego numeru wersji w pliku manifestu wdrożenia. To podejście jest zalecane do celów produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Uaktualnianie stopniowe obrazu znacznika

Jeśli używasz znaczników stopniowych we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1,0**), musisz wymusić na urządzeniu środowisko uruchomieniowe kontenera, aby ściągnąć najnowszą wersję obrazu. 

Usuń lokalną wersję obrazu z urządzenia usługi IoT Edge. Na maszynach z systemem Windows Dezinstalacja demona zabezpieczeń usuwa również obrazy środowiska uruchomieniowego, więc nie trzeba ponownie wykonać tego kroku. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Aby usunąć obrazy, może być konieczne użycie flagi Force `-f`. 

Usługa IoT Edge pobierania najnowszych wersji obrazów środowiska uruchomieniowego i automatyczne uruchamianie ich na swoim urządzeniu ponownie. 

### <a name="update-a-specific-tag-image"></a>Aktualizuj obraz konkretny tag

Jeśli używasz określonych tagów we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.7**), wystarczy zaktualizować tag w manifeście wdrożenia i zastosować zmiany na urządzeniu. 

W Azure Portal obrazy wdrożeń środowiska uruchomieniowego są deklarowane w sekcji **Konfigurowanie ustawień zaawansowanego środowiska uruchomieniowego Edge** . 

![Skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge](./media/how-to-update-iot-edge/configure-runtime.png)

W manifeście wdrożenia JSON zaktualizuj obrazy modułów w sekcji **systemModules** . 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Aktualizacja do wersji Release Candidate

Azure IoT Edge regularnie zwalnia nowe wersje usługi IoT Edge. Przed każdą stabilną wersją jest dostępna co najmniej jedna wersja Release Candidate (RC). Wersje RC obejmują wszystkie planowane funkcje wydania, ale nadal przechodzą przez procesy testowania i sprawdzania poprawności wymagane do stabilnej wersji. Jeśli chcesz przetestować nową funkcję wcześniej, możesz zainstalować wersję RC i przekazać opinię za pomocą usługi GitHub. 

Wersje Release Candidate są zgodne z tą samą konwencją numerowania wydań, ale mają **-RC** Plus numer przyrostowy dołączony do końca. Kandydatów wersji można zobaczyć na tej samej liście wersji [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) , co w przypadku stabilnych wersji. Na przykład Znajdź **1.0.7-RC1** i **1.0.7-RC2**, dwie wersje kandydatów, które zostały dostarczone przed **1.0.7**. Możesz również sprawdzić, czy wersje RC są oznaczone etykietami **wersji wstępnej** . 

W ramach wersji zapoznawczych wersje Release Candidate nie są uwzględniane w najnowszej wersji docelowej zwykłych instalatorów. Zamiast tego należy ręcznie określić zasoby dla wersji RC, która ma zostać przetestowana. W zależności od systemu operacyjnego urządzenia IoT Edge należy skorzystać z poniższych sekcji w celu zaktualizowania IoT Edge do określonej wersji:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z najnowszymi [wersjami Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniemi w [blogu Internet rzeczy](https://azure.microsoft.com/blog/topics/internet-of-things/) 