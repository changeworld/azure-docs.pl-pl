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
ms.openlocfilehash: 41d544fd23d258393cc83ea09371332655223581
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203942"
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
  "content": {
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
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"CapAdd\":[\"NET_ADMIN\"]}}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
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

11. Przejrzyj swoje informacje na temat wdrażania i manifestu.  Powinien on wyglądać podobnie powyżej manifest wdrożenia.  Wybierz **przesłać**.

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

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć bliźniaczej reprezentacji OPC od podstaw, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Wdrażanie bliźniaczej reprezentacji OPC do istniejącego projektu](howto-opc-twin-deploy-existing.md)