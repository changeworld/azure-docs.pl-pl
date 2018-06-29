---
title: Wdrażanie usługi Azure IoT krawędzi modułów (CLI) | Dokumentacja firmy Microsoft
description: Użyj rozszerzenia IoT Azure CLI 2.0 na potrzeby wdrażania modułów urządzenia IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98a4be02188f7e0462979792a6061d535a64a18d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095979"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Wdrażanie modułów Azure IoT krawędzi 2.0 interfejsu wiersza polecenia platformy Azure

Po utworzeniu krawędzi IoT modułów z logiki biznesowej, którą chcesz wdrożyć je na urządzeniach do działania na krawędzi. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, można wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy. 

[Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to międzyplatformowe narzędzie wiersza polecenia typu open-source służące do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Umożliwia zarządzanie zasobami, inicjowania obsługi administracyjnej wystąpień usługi urządzenia i połączone w koncentratory fabrycznej Centrum IoT Azure. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure 2.0 o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule pokazano, jak utworzyć manifest rozmieszczenia JSON, a następnie użyć do wypychania wdrażanie do urządzenia IoT. Informacji o tworzeniu wdrożenia, które jest przeznaczony dla wielu urządzeń według ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w Twojej subskrypcji platformy Azure. 
* [Urządzenia IoT](how-to-register-device-cli.md) ze środowiskiem uruchomieniowym krawędzi IoT zainstalowane.
* Zainstalowany w środowisku [interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Skonfiguruj manifest wdrażania

Manifest rozmieszczenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, jak dane przepływają między modułami i odpowiednie właściwości twins modułu. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia oraz sposób ich tworzenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

Aby wdrożyć modułów za pomocą usługi Azure CLI 2.0, Zapisz lokalnie manifest rozmieszczenia jako plik .txt. Ścieżka pliku użyje w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia. 

Oto manifestu podstawowego wdrożenia z jednego modułu jako przykład:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
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

## <a name="deploy-to-your-device"></a>Wdrażanie do urządzenia

Moduły można wdrożyć na urządzeniu, stosując skonfigurowana z informacjami o module manifest wdrażania. 

Użyj następującego polecenia, aby zastosować konfigurację do urządzenia IoT:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr id urządzenia jest rozróżniana wielkość liter. Punktów zawartości parametru do wdrożenia manifest zapisany plik. 

## <a name="view-modules-on-your-device"></a>Widok modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu, można wyświetlić wszystkie z nich przy użyciu następującego polecenia: 

Wyświetl moduły znajdujące się na urządzeniu usługi IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr id urządzenia jest rozróżniana wielkość liter.

   ![Wyświetlanie listy modułów](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md)