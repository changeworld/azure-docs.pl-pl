---
title: Aktualizacja wersji usługi IoT Edge na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak zaktualizować urządzeń usługi IoT Edge, aby uruchamiać najnowsze wersje demona zabezpieczeń i środowisko uruchomieniowe usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3dd7f78362b5f5c99dc4a74fe0a32c4d26be5b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125937"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska uruchomieniowego

Jak usługa IoT Edge wydaje nowe wersje, należy zaktualizować urządzeniach usługi IoT Edge w taki sposób, aby uzyskać najnowsze funkcje i poprawki zabezpieczeń. Ten artykuł zawiera informacje o sposobie aktualizowania urządzenia usługi IoT Edge, gdy dostępna jest nowa wersja. 

Dwa składniki urządzenia usługi IoT Edge należy zaktualizować, jeśli chcesz przejść do nowszej wersji. Pierwsza to demona zabezpieczeń, który działa lokalnie na urządzeniu i rozpoczyna się moduły środowiska uruchomieniowego, gdy urządzenie zostanie uruchomione. Obecnie usługa demona zabezpieczeń można aktualizować tylko z samego urządzenia. Drugi składnik to środowisko uruchomieniowe, składa się z Centrum usługi IoT Edge i moduły agenta usługi IoT Edge. W zależności od strukturą wdrożenia środowisko uruchomieniowe może zostać zaktualizowana z urządzenia lub zdalnie. 

Aby uzyskać najnowszą wersję usługi Azure IoT Edge, zobacz [wersje usługi Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Jeśli używasz usługi Azure IoT Edge na urządzeniu z systemem Windows, nie są uaktualniane do wersji 1.0.5, jedną z następujących dotyczy urządzenia: 
>* Urządzenie nie zostały uaktualnione do Windows 17763 kompilacji. Wersja usługi IoT Edge 1.0.5 nie obsługuje Windows tworzy starsze niż 17763.
>* Moduły języka Java lub Node.js możesz uruchomić na urządzeniu z systemem Windows. Pomiń wersji 1.0.5, nawet jeśli urządzenie Windows zostały zaktualizowane do najnowszej kompilacji. 
>
>Aby uzyskać więcej informacji na temat usługi IoT Edge wersji 1.0.5 zobacz [informacje o wersji 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Aby uzyskać więcej informacji na temat uniemożliwić własne narzędzia programistyczne aktualizowanie do najnowszej wersji, zobacz [blog deweloperów IoT](https://devblogs.microsoft.com/iotdev/).


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

Na urządzeniach z systemami Windows należy użyć skryptu PowerShell do odinstalowanie i ponowne zainstalowanie demona zabezpieczeń. Skrypt instalacji automatycznie pobiera najnowszą wersję demona zabezpieczeń. 

Odinstaluj demona zabezpieczeń w sesji programu PowerShell administratora. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

Uruchamianie `Uninstall-SecurityDaemon` polecenie bez parametrów usuwa tylko demona zabezpieczeń z urządzenia, oraz dwa obrazy kontenera środowiska uruchomieniowego. Plik config.yaml jest przechowywany na urządzeniu, a także dane z aparatu kontenera Moby. Utrzymywanie oznacza informacje o konfiguracji, że nie musisz podać parametry połączenia lub informacji usługi Device Provisioning Service dla Twojego urządzenia ponownie podczas procesu instalacji. 

Zainstaluj ponownie demona zabezpieczeń, w zależności od tego, czy urządzenia usługi IoT Edge korzysta z kontenerów Windows lub kontenerów systemu Linux. Zastąp wyrażenie **\<Windows lub Linux\>** z systemami operacyjnymi odpowiedniego kontenera. Użyj **- ExistingConfig** flagę, aby wskazać istniejący plik config.yaml na urządzeniu. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

Jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, pobierania pliku odpowiednią windows.zip iotedged [wersji usługi IoT Edge](https://github.com/Azure/azure-iotedge/releases). Następnie należy użyć `-OfflineInstallationPath` parametru, aby wskazywała lokalizację pliku. Aby uzyskać więcej informacji, zobacz [instalacji w trybie Offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aktualizuj kontenerów środowiska wykonawczego

Sposób aktualizacji agenta usługi IoT Edge i IoT Edge hub kontenerów, zależy od tego, czy używać stopniowe tagów (na przykład 1.0) lub konkretne znaczniki (na przykład 1.0.2) w danym wdrożeniu. 

Zapoznaj się z wersją agenta usługi IoT Edge i moduły Centrum usługi IoT Edge obecnie na twoim urządzeniu, za pomocą poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub`. 

  ![Znajdź wersję kontenera w dziennikach](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Omówienie usługi IoT Edge tagów

Agent usługi IoT Edge i IoT Edge hub obrazy są oznaczane za pomocą wersji usługi IoT Edge, które są skojarzone. Istnieją dwa różne sposoby przy użyciu obrazów środowiska uruchomieniowego za pomocą tagów: 

* **Stopniowe tagi** — Użyj tylko dwie pierwsze wartości numer wersji można pobrać najnowszego obrazu, który odpowiada te cyfry. Na przykład 1.0 jest aktualizowany zawsze wtedy, gdy istnieje nowa wersja, aby wskazywał najnowszą wersję 1.0.x. Jeśli kontener środowiska uruchomieniowego na urządzeniu usługi IoT Edge ściąga obraz ponownie, moduły środowiska uruchomieniowego są aktualizowane do najnowszej wersji. To podejście jest zalecane do celów programistycznych. Wdrożenia z domyślnego portalu Azure do stopniowego tagów. 
* **Określone znaczniki** — Użyj wszystkich trzech wartości numer wersji można jawnie ustawić wersję obrazu. Na przykład 1.0.2 nie zmienią się od początkowego wydania. Gdy wszystko będzie gotowe do aktualizacji, można zadeklarować nowego numeru wersji w pliku manifestu wdrożenia. To podejście jest zalecane do celów produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Uaktualnianie stopniowe obrazu znacznika

Jeśli używasz stopniowe tagów w danym wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0**), a następnie należy wymusić kontener środowiska uruchomieniowego na urządzeniu, aby ściągnąć najnowszą wersję obrazu. 

Usuń lokalną wersję obrazu z urządzenia usługi IoT Edge. Na maszynach Windows odinstalowywanie demona zabezpieczeń powoduje również usunięcie obrazów środowiska uruchomieniowego, dzięki czemu nie trzeba wykonać ten krok ponownie. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Może być konieczne użycie uruchomionego z wymuszeniem `-f` flagę, aby usunąć obrazy. 

Usługa IoT Edge pobierania najnowszych wersji obrazów środowiska uruchomieniowego i automatyczne uruchamianie ich na swoim urządzeniu ponownie. 

### <a name="update-a-specific-tag-image"></a>Aktualizuj obraz konkretny tag

Korzystając z określonymi tagami w danym wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.2**), a następnie wszystko, czego potrzebujesz, aby zrobić to tag w manifeście wdrożenia aktualizacji i Zastosuj zmiany do Twojego urządzenia. 

W witrynie Azure portal, obrazy do wdrażania środowiska uruchomieniowego są deklarowane w **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji. 

![Skonfiguruj zaawansowane ustawienia usługi edge środowiska uruchomieniowego](./media/how-to-update-iot-edge/configure-runtime.png)

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

Na bieżąco najnowsze aktualizacje i ogłoszenia w [blog Internetu rzeczy](https://azure.microsoft.com/blog/topics/internet-of-things/) 