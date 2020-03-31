---
title: Wdrażanie modułów z wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure — usługa Azure IoT Edge
description: Użyj interfejsu wiersza polecenia platformy Azure z rozszerzeniem IoT platformy Azure, aby wypchnąć moduł usługi IoT Edge z centrum IoT Hub do urządzenia usługi IoT Edge, skonfigurowanego przez manifest wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 619ba7cb2d99e0137fd1834096dd5b66ffcd6ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240378"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Wdrażanie modułów usługi Azure IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure

Po utworzeniu modułów usługi IoT Edge z logiką biznesową, chcesz wdrożyć je na urządzeniach do pracy na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, można wdrożyć je wszystkie naraz i zadeklarować reguły routingu, które je łączą.

[Narzędzie interfejsu wiersza](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) polecenia platformy Azure to narzędzie wiersza polecenia platformy open source do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi inicjowania obsługi administracyjnej urządzeń i połączonych koncentratorów po wyjęciu z pudełka. Nowe rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule pokazano, jak utworzyć manifest wdrożenia JSON, a następnie użyć tego pliku do wypchnięcia wdrożenia do urządzenia usługi IoT Edge. Aby uzyskać informacje dotyczące tworzenia wdrożenia przeznaczonego dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [Wdrażanie i monitorowanie modułów usługi IoT Edge na dużą skalę](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT w](../iot-hub/iot-hub-create-using-cli.md) subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-with-the-azure-cli) z zainstalowanym czasem wykonywania IoT Edge.
* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w twoim środowisku. Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Aby uzyskać więcej informacji na temat działania manifestów wdrażania i sposobu ich tworzenia, zobacz Opis sposobu [użycia, skonfigurowania i ponownego użycia modułów usługi IoT Edge.](module-composition.md)

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, zapisz manifest wdrożenia lokalnie jako plik .json. Ścieżka pliku będzie używana w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

Oto podstawowy manifest wdrażania z jednym modułem jako przykład:

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
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Moduły można wdrożyć na urządzeniu, stosując manifest wdrożenia skonfigurowany z informacjami o module.

Zmień katalogi w folder, w którym jest zapisywany manifest wdrożenia. Jeśli użyto jednego z szablonów usługi VS Code `deployment.json` IoT Edge, użyj pliku w `deployment.template.json` folderze **konfiguracji** katalogu rozwiązania, a nie pliku.

Użyj następującego polecenia, aby zastosować konfigurację do urządzenia Usługi IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

W parametrze identyfikatora urządzenia rozróżniana jest wielkość liter. Parametr zawartości wskazuje zapisany plik manifestu wdrożenia.

   ![az iot krawędzi zestaw modułów wyjścia](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu można wyświetlić je za pomocą następującego polecenia:

Wyświetl moduły znajdujące się na urządzeniu usługi IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

W parametrze identyfikatora urządzenia rozróżniana jest wielkość liter.

   ![az iot hub module-identity list output az iot hub module-identity list output az iot](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
