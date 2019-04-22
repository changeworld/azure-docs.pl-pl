---
title: Jak wdrożyć OPC bliźniaczej reprezentacji modułu na platformę Azure od podstaw | Dokumentacja firmy Microsoft
description: Jak wdrożyć bliźniaczej reprezentacji OPC od podstaw.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491360"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Wdrażanie OPC bliźniaczej reprezentacji modułu i zależności od podstaw

OPC bliźniaczej reprezentacji modułu jest uruchamiany na urządzeniach brzegowych IoT i udostępnia kilka usług brzegowych bliźniaczej reprezentacji urządzenia OPC i usługi rejestru. 

Dostępnych jest kilka opcji, aby wdrożyć moduły do Twojej [usługi Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) bramy między nimi

- [Wdrażanie z bloku usługi IoT Edge w witrynie Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Wdrażanie przy użyciu interfejsu wiersza polecenia AZ](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrażania oraz instrukcje, zobacz GitHub [repozytorium](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifest wdrożenia

Wszystkie moduły są wdrażane przy użyciu manifest wdrożenia.  Manifest przykład, można wdrożyć obydwa elementy [wydawca OPC](https://github.com/Azure/iot-edge-opc-publisher) i [bliźniaczej reprezentacji OPC](https://github.com/Azure/azure-iiot-opc-twin-module) znajdują się poniżej.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Wdrażanie z witryny Azure portal

Najprostszym sposobem, aby wdrożyć moduły urządzenia bramy usługi Azure IoT Edge jest w witrynie Azure portal.  

### <a name="prerequisites"></a>Wymagania wstępne

1. Wdrażanie bliźniaczej reprezentacji OPC [zależności](howto-opc-twin-deploy-dependencies.md) i pobierane wynikowy `.env` pliku. Należy pamiętać, wdrożonych `hub name` z `PCS_IOTHUBREACT_HUB_NAME` zmiennej w wynikowym `.env` pliku.

2. Rejestrowanie i rozpocząć [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) lub [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) brama usługi IoT Edge i zwróć uwagę, jego `device id`.

### <a name="deploy-to-an-edge-device"></a>Wdrażanie na urządzeniu usługi edge

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do Centrum IoT hub.

2. Wybierz **usługi IoT Edge** z menu po lewej stronie.

3. Kliknij identyfikator urządzenie docelowe z listy urządzeń.

4. Wybierz pozycję **Ustaw moduły**.

5. W **moduły wdrożeń** części strony wybierz **Dodaj** i **moduł usługi IoT Edge.**

6. W **niestandardowy moduł usługi IoT Edge** użyj okna dialogowego `opctwin` jako nazwy modułu, a następnie określ kontener *identyfikator URI obrazu* jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Jako *opcje tworzenia* Użyj następujące dane JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Wypełnij pola opcjonalne, jeśli to konieczne. Więcej informacji na temat kontenerów opcje, zasady ponownego uruchamiania, tworzenia i zobacz żądany stan [EdgeAgent żądane właściwości](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczą reprezentację modułu zobacz [Definiuj lub aktualizacji żądane właściwości](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Wybierz **Zapisz** i powtórz krok **5**.  

8. W oknie dialogowym moduł usługi IoT Edge niestandardowe, należy użyć `opcpublisher` jako nazwę modułu i kontener *identyfikator URI obrazu* jako 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Jako *opcje tworzenia* Użyj następujące dane JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Wybierz **Zapisz** i następnie **dalej** aby przejść do sekcji trasy.

10. Na karcie trasy Wklej następujący tekst 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    i wybierz **dalej**

11. Przejrzyj swoje informacje na temat wdrażania i manifestu.  Powinien on wyglądać podobnie powyżej manifest wdrożenia.  Wybierz pozycję **Prześlij**.

12. Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich w **szczegóły urządzenia** strony portalu. Ta strona wyświetla nazwę każdego modułu wdrożone, a także przydatne informacje, takie jak kod stanu i zakończenia wdrożenia.

## <a name="deploying-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [tutaj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Szybki start

1. Zapisz powyżej manifest wdrożenia do `deployment.json` pliku.  

2. Aby zastosować konfigurację do urządzenia usługi IoT Edge, użyj następującego polecenia:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` Parametrów jest rozróżniana wielkość liter. Punktów zawartości parametru do wdrożenia w manifeście zapisany plik. 
    ![dane wyjściowe zestawu modułów usługi IoT Edge az](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich za pomocą następującego polecenia:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Parametr ID urządzeń jest uwzględniana wielkość liter. ![dane wyjściowe az iot hub tożsamości modułu listy](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>Uruchamianie i debugowanie lokalnie

Problemy z rozwiązywania problemów i debugowanie jest przydatne do uruchamiania moduły usługi Edge lokalnie przy użyciu [IoT Edge rozwoju symulator](https://github.com/Azure/iotedgehubdev).  Usługa udostępnia środowisko programowania lokalnego symulatora do tworzenia, opracowywania, testowania, uruchamiania i debugowania moduły usługi Azure IoT Edge i rozwiązań za pomocą tego samego bity/kodu, które są używane w środowisku produkcyjnym.

### <a name="prerequisites"></a>Wymagania wstępne

1. Wdrażanie bliźniaczej reprezentacji OPC [zależności](howto-opc-twin-deploy-dependencies.md).

2. Zainstaluj [Docker CE (18.02.0+)](https://www.docker.com/community-edition) na [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) lub [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Zainstaluj [narzędzia Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (wymagane tylko dla **Linux**. Redagowania już została uwzględniona w systemach operacyjnych Windows/macOS Docker CE instalacji)

4. Zainstaluj [Python (w wersji 2.7 / 3.5+) oraz publicznego adresu IP](https://www.python.org/)

5. Zainstaluj iotedgehubdev, uruchamiając poniższe polecenie w terminalu

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Zainstaluj `iotedgehubdev` do **głównego** w systemie Linux/macOS (*nie należy używać "--użytkownika" opcja w poleceniu "pip install"*).
> Upewnij się, że nie środowiska uruchomieniowego usługi Azure IoT Edge, uruchomione na tym samym komputerze przy użyciu iotedgehubdev, ponieważ wymagają one tych samych portów.

### <a name="quickstart"></a>Szybki start

1. Postępuj zgodnie z instrukcjami, aby [tworzenie urządzenia Edge w witrynie Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Skopiuj parametry połączenia urządzenia usługi edge.

2. Skonfiguruj symulator przy użyciu parametrów połączenia usługi edge.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Kopiuj powyżej manifestu do `deployment.json` pliku, w tym samym folderze.  Rozpocząć wdrażanie w symulatorze, używając

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Zatrzymaj przy użyciu symulatora

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć bliźniaczej reprezentacji OPC od podstaw, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Wdrażanie bliźniaczej reprezentacji OPC do istniejącego projektu](howto-opc-twin-deploy-existing.md)