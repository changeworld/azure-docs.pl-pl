---
title: Wdrażać moduły z wiersza polecenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, aby wdrożyć moduły na urządzeniu usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 766b51f208e7e8f4a49109e32864f2726b8ccd63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126396"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Wdrożyć moduły usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy.

[Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) jest typu open-source wielu platformach narzędzia wiersza polecenia do zarządzania zasobami platformy Azure, takich jak usługi IoT Edge. Umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi device provisioning i połączonymi centrami po gotowych. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure przy użyciu funkcji, takich jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule pokazano, jak utworzyć manifest wdrożenia JSON, a następnie użyć do wypychania wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacji o tworzeniu wdrożenia, który jest przeznaczony dla wielu urządzeń, na podstawie ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-using-cli.md) w subskrypcji platformy Azure.
* [Urządzenie usługi IoT Edge](how-to-register-device-cli.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

Aby wdrożyć moduły przy użyciu wiersza polecenia platformy Azure, Zapisz manifest wdrażania lokalnie jako plik JSON. Ścieżka pliku zostaną użyte w następnej sekcji, po uruchomieniu polecenia, aby zastosować konfigurację do Twojego urządzenia.

Poniżej przedstawiono manifestu podstawowego wdrożenia za pomocą jednego modułu, na przykład:

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
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
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
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Możesz wdrożyć moduły do Twojego urządzenia, stosując manifestu wdrażania, który został skonfigurowany z informacjami o module.

Przejdź do folderu, w którym jest zapisany manifest wdrożenia. Jeśli używasz jednego z szablonów program VS Code IoT Edge, użyj `deployment.json` w pliku **config** folder katalogu rozwiązania i nie `deployment.template.json` pliku.

Aby zastosować konfigurację do urządzenia usługi IoT Edge, użyj następującego polecenia:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr id urządzeń jest uwzględniana wielkość liter. Punktów zawartości parametru do wdrożenia w manifeście zapisany plik.

   ![AZ iot edge zestaw modułów w danych wyjściowych](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich za pomocą następującego polecenia:

Wyświetl moduły znajdujące się na urządzeniu usługi IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr id urządzeń jest uwzględniana wielkość liter.

   ![dane wyjściowe az iot hub tożsamości modułu listy](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
