---
title: Jak wdrożyć moduł OPC Twin dla platformy Azure od podstaw | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wdrażania modułów bliźniaczych oczek OPC od podstaw przy użyciu bloku usługi IoT edge witryny Azure portal, a także przy użyciu interfejsu wiersza polecenia AZ.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241057"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Wdrażanie modułu OPC Twin i zależności od podstaw

Moduł OPC Twin działa na ioT Edge i zapewnia kilka usług brzegowych do bliźniaczej reprezentacji urządzenia OPC i usług rejestru. 

Istnieje kilka opcji wdrażania modułów w bramie [usługi Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, wśród nich

- [Wdrażanie z bloku Usługi IoT Edge usługi Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Wdrażanie przy użyciu interfejsu wiersza polecenia AZ](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrażania i instrukcji, zobacz [repozytorium](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifest wdrożenia

Wszystkie moduły są wdrażane przy użyciu manifestu wdrażania.  Poniżej przedstawiono przykładowy manifest do wdrożenia zarówno [programu OPC Publisher,](https://github.com/Azure/iot-edge-opc-publisher) jak i [OPC Twin.](https://github.com/Azure/azure-iiot-opc-twin-module)

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
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
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

## <a name="deploying-from-azure-portal"></a>Wdrażanie w witrynie Azure portal

Najprostszym sposobem wdrożenia modułów na urządzeniu bramy usługi Azure IoT Edge jest za pośrednictwem witryny Azure portal.  

### <a name="prerequisites"></a>Wymagania wstępne

1. Wdrażanie [zależności](howto-opc-twin-deploy-dependencies.md) OPC Twin i `.env` uzyskał wynikowy plik. Zanotuj `hub name` wdrożoną zmienną `PCS_IOTHUBREACT_HUB_NAME` `.env` w pliku wynikowym.

2. Zarejestruj i uruchom bramę [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) lub [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) `device id`IoT Edge i zanotuj jej .

### <a name="deploy-to-an-edge-device"></a>Wdrażanie na urządzeniu brzegowym

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do centrum IoT Hub.

2. Wybierz pozycję **IoT Edge** z menu po lewej stronie.

3. Kliknij identyfikator urządzenia docelowego z listy urządzeń.

4. Wybierz pozycję **Ustaw moduły**.

5. W sekcji **Moduły wdrażania** na stronie wybierz pozycję **Dodaj** i **Moduł krawędzi IoT.**

6. W oknie dialogowym **Moduł niestandardowy usługi IoT Edge** służy `opctwin` jako nazwa modułu, a następnie określ identyfikator *URI obrazu* kontenera jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Jako *opcje tworzenia kontenera*użyj następującego JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   W razie potrzeby wypełnij pola opcjonalne. Aby uzyskać więcej informacji na temat opcji tworzenia kontenera, zasad ponownego uruchamiania i żądanego stanu, zobacz [Polecenie EdgeAgent żądane właściwości](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczej reprezentacji modułu, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Wybierz **zapisz** i powtórz krok **5**.  

8. W oknie dialogowym Moduł niestandardowy `opcpublisher` usługi IoT Edge użyj jako nazwy modułu i *identyfikatora URI obrazu* kontenera 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Jako *opcje tworzenia kontenera*użyj następującego JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Wybierz **pozycję Zapisz,** a następnie **pozycję Dalej,** aby przejść do sekcji Trasy.

10. Na karcie Trasy wklej następujące 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    i wybierz przycisk **Dalej**

11. Przejrzyj informacje o wdrożeniu i manifest.  Powinien wyglądać jak powyższy manifest wdrażania.  Wybierz pozycję **Prześlij**.

12. Po wdrożeniu modułów na urządzeniu możesz wyświetlić je wszystkie na stronie **Szczegóły urządzenia** w portalu. Na tej stronie wyświetlana jest nazwa każdego wdrożonego modułu, a także przydatne informacje, takie jak stan wdrożenia i kod zakończenia.

## <a name="deploying-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [w tym miejscu](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Szybki start

1. Zapisz powyższy manifest `deployment.json` wdrożenia w pliku.  

2. Użyj następującego polecenia, aby zastosować konfigurację do urządzenia Usługi IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   W `device id` przypadku przypadku przypadku tej jest rozróżniana wielkość liter. Parametr zawartości wskazuje zapisany plik manifestu wdrożenia. 
    ![az IoT Edge zestaw modułów wyjściowych](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Po wdrożeniu modułów na urządzeniu można wyświetlić je za pomocą następującego polecenia:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   W parametrze identyfikatora urządzenia rozróżniana jest wielkość liter. ![az iot hub module-identity list output az iot hub module-identity list output az iot](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy nauczyłeś się wdrażać OPC Twin od podstaw, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Wdrażanie programu OPC Twin w istniejącym projekcie](howto-opc-twin-deploy-existing.md)
