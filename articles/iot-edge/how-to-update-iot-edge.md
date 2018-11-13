---
title: Zaktualizuj urządzenia do najnowszej wersji usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak zaktualizować urządzeń usługi IoT Edge, aby uruchamiać najnowsze wersje demona zabezpieczeń i środowisko uruchomieniowe usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1522d9c9bc4fda178d8571fb57cb9c94ed1044ae
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567795"
---
# <a name="update-the-iot-edge-runtime"></a>Aktualizacja środowiska uruchomieniowego usługi IoT Edge

Jak usługa IoT Edge wydaje nowe wersje, należy zaktualizować urządzenia usługi IoT Edge, aby mieć najnowsze funkcje i poprawki zabezpieczeń. Ten artykuł zawiera informacje o sposobie aktualizowania urządzenia usługi IoT Edge, gdy dostępna jest nowa wersja. 

Dwa składniki urządzenia usługi IoT Edge należy zaktualizować, jeśli chcesz przejść do nowszej wersji. Pierwsza to demona zabezpieczeń, który działa lokalnie na urządzeniu i rozpoczyna się środowiska uruchomieniowego, gdy urządzenie zostanie uruchomione. Obecnie usługa demona zabezpieczeń można aktualizować tylko z samego urządzenia. Drugi składnik to środowisko uruchomieniowe, składa się z Centrum usługi Edge i moduły agenta usługi Edge. W zależności od strukturą wdrożenia środowisko uruchomieniowe może zostać zaktualizowana z urządzenia lub zdalnie. 

Aby uzyskać najnowszą wersję usługi Azure IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizacja demona zabezpieczeń

Demon zabezpieczeń usługi IoT Edge jest natywny składnik, który musi zostać zaktualizowana przy użyciu Menedżera pakietów na urządzeniu usługi IoT Edge. 

Zapoznaj się z wersją demona zabezpieczeń na urządzeniu z za pomocą polecenia `iotedge version`. 

### <a name="linux-devices"></a>Urządzeniami z systemem Linux

Na urządzeniach z systemem Linux należy użyć polecenia apt-get lub Menedżera odpowiedni pakiet do zaktualizowania demona zabezpieczeń. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Urządzenia Windows

Na urządzeniach z systemami Windows należy użyć skryptu PowerShell do odinstalowanie i ponowne zainstalowanie demona zabezpieczeń. Skrypt instalacji automatycznie pobiera najnowszą wersję demona zabezpieczeń. Musisz podać parametry połączenia dla Twojego urządzenia ponownie podczas procesu instalacji. 

Odinstaluj demona zabezpieczeń w sesji programu PowerShell administratora. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

Zainstaluj ponownie demona zabezpieczeń, w zależności od tego, czy urządzenia usługi IoT Edge korzysta z kontenerów Windows lub kontenerów systemu Linux. Zastąp wyrażenie **\<Windows lub Linux\>** przy użyciu jednego z systemów operacyjnych kontenera. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>Aktualizuj kontenerów środowiska wykonawczego

Sposób aktualizacji agenta usługi Edge i przeglądarki Edge hub kontenerów, zależy od tego, czy używać stopniowe tagów (na przykład 1.0) lub konkretne znaczniki (na przykład 1.0.2) w danym wdrożeniu. 

Zapoznaj się z wersją agenta usługi IoT Edge i moduły Centrum usługi Edge obecnie na twoim urządzeniu, za pomocą poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub`. 

  ![Wyświetl wersję kontenera](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Omówienie usługi IoT Edge tagów

Agent usługi Edge i przeglądarki Edge hub obrazy są oznaczane za pomocą wersji usługi IoT Edge, które są skojarzone. Istnieją dwa różne sposoby przy użyciu obrazów środowiska uruchomieniowego za pomocą tagów: 

* **Stopniowe tagi** — Użyj tylko dwie pierwsze wartości numer wersji można pobrać najnowszego obrazu, który odpowiada te cyfry. Na przykład 1.0 jest aktualizowany zawsze wtedy, gdy istnieje nowa wersja, aby wskazywał najnowszą wersję 1.0.x. Jeśli kontener środowiska uruchomieniowego na urządzeniu usługi IoT Edge ściąga obraz ponownie, moduły środowiska uruchomieniowego są aktualizowane do najnowszej wersji. To podejście jest zalecane do celów programistycznych. Wdrożenia z domyślnego portalu Azure do stopniowego tagów. 
* **Określone znaczniki** — Użyj wszystkich trzech wartości numer wersji można jawnie ustawić wersję obrazu. Na przykład 1.0.2 nie zmienią się od początkowego wydania. Gdy wszystko będzie gotowe do aktualizacji, można zadeklarować nowego numeru wersji w pliku manifestu wdrożenia. To podejście jest zalecane do celów produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Uaktualnianie stopniowe obrazu znacznika

Jeśli używasz stopniowe tagów w danym wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0**), a następnie należy wymusić kontener środowiska uruchomieniowego na urządzeniu, aby ściągnąć najnowszą wersję obrazu. 

Usuń lokalną wersję obrazu z urządzenia usługi IoT Edge. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Może być konieczne użycie uruchomionego z wymuszeniem `-f` flagę, aby usunąć obrazy. 

Usługa IoT Edge pobierania najnowszych wersji obrazów środowiska uruchomieniowego i automatyczne uruchamianie ich na swoim urządzeniu ponownie. 

### <a name="update-a-specific-tag-image"></a>Aktualizuj obraz konkretny tag

Korzystając z określonymi tagami w danym wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.2**), a następnie wszystko, czego potrzebujesz, aby zrobić to tag w manifeście wdrożenia aktualizacji i Zastosuj zmiany do Twojego urządzenia. 

W witrynie Azure portal, obrazy do wdrażania środowiska uruchomieniowego są deklarowane w **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji. 

[Skonfiguruj zaawansowane ustawienia usługi edge środowiska uruchomieniowego](./media/how-to-update-iot-edge/configure-runtime.png)

W manifeście wdrożenia JSON, należy zaktualizować obrazy modułu w **systemModules** sekcji. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Kolejne kroki

Wyświetl najnowsze [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Bądź na bieżąco przy użyciu najnowszych aktualizacji i ogłoszenia w [blog Internetu rzeczy](https://azure.microsoft.com/blog/topics/internet-of-things/) 