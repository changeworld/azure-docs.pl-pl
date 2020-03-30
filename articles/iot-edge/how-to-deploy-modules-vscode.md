---
title: Wdrażanie modułów z programu Visual Studio Code — Usługa Azure IoT Edge
description: Użyj programu Visual Studio Code z narzędziami Usługi Azure IoT, aby wypchnąć moduł usługi IoT Edge z centrum IoT Hub do urządzenia usługi IoT Edge, skonfigurowanego przez manifest wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209209"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Wdrażanie modułów usługi Azure IoT Edge z kodu programu Visual Studio

Po utworzeniu modułów usługi IoT Edge z logiką biznesową, chcesz wdrożyć je na urządzeniach do pracy na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, można wdrożyć je wszystkie naraz i zadeklarować reguły routingu, które je łączą.

W tym artykule pokazano, jak utworzyć manifest wdrożenia JSON, a następnie użyć tego pliku do wypchnięcia wdrożenia do urządzenia usługi IoT Edge. Aby uzyskać informacje dotyczące tworzenia wdrożenia przeznaczonego dla wielu urządzeń na podstawie ich tagów udostępnionych, zobacz [Wdrażanie modułów usługi IoT Edge na dużą skalę przy użyciu programu Visual Studio Code](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-with-visual-studio-code) z zainstalowanym czasem wykonywania IoT Edge.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla programu Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Aby uzyskać więcej informacji na temat działania manifestów wdrażania i sposobu ich tworzenia, zobacz Opis sposobu [użycia, skonfigurowania i ponownego użycia modułów usługi IoT Edge.](module-composition.md)

Aby wdrożyć moduły przy użyciu programu Visual Studio Code, zapisz manifest wdrożenia lokalnie jako plik . JSON. Ścieżka pliku będzie używana w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

Oto podstawowy manifest wdrażania z jednym modułem jako przykład:

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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

Rozszerzenia IoT usługi Azure dla programu Visual Studio Code umożliwiają wykonywanie operacji za pomocą centrum IoT hub. Aby te operacje działały, musisz zalogować się do konta platformy Azure i wybrać centrum IoT, nad którymi pracujesz.

1. W programie Visual Studio Code otwórz widok **Eksploratora.**

1. U dołu Eksploratora rozwiń sekcję **Centrum Usługi Azure IoT.**

   ![Rozwiń sekcję Centrum Usługi Azure IoT](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kliknij **...** w nagłówku sekcji **Usługi Azure IoT Hub.** Jeśli wielokropek nie jest widoczny, umieść wskaźnik myszy na nagłówku.

1. Wybierz **pozycję Wybierz Centrum IoT**.

1. Jeśli nie jesteś zalogowany do konta platformy Azure, postępuj zgodnie z instrukcjami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz centrum IoT.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Moduły można wdrożyć na urządzeniu, stosując manifest wdrożenia skonfigurowany z informacjami o module.

1. W widoku Eksploratora kodu programu Visual Studio rozwiń sekcję **Centrum Usługi Azure IoT,** a następnie rozwiń węzeł **Urządzenia.**

1. Kliknij prawym przyciskiem myszy urządzenie usługi IoT Edge, które chcesz skonfigurować za pomocą manifestu wdrażania.

    > [!TIP]
    > Aby potwierdzić, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie IoT Edge zawiera te dwa moduły.

1. Wybierz **pozycję Utwórz wdrożenie dla pojedynczego urządzenia**.

1. Przejdź do pliku JSON manifestu wdrożenia, którego chcesz użyć, a następnie kliknij przycisk **Wybierz manifest wdrożenia krawędzi**.

   ![Wybierz manifest wdrażania krawędzi](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Wyniki wdrożenia są drukowane w danych wyjściowych kodu VS. Pomyślne wdrożenia są stosowane w ciągu kilku minut, jeśli urządzenie docelowe jest uruchomione i połączone z Internetem.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu możesz wyświetlić je wszystkie w sekcji **Centrum Usługi Azure IoT** Hub. Wybierz strzałkę obok urządzenia IoT Edge, aby ją rozwinąć. Zostaną wyświetlone wszystkie aktualnie uruchomione moduły.

Jeśli niedawno wdrożono nowe moduły na urządzeniu, umieść wskaźnik myszy na nagłówku sekcji **Urządzenia usługi Azure IoT Hub** i wybierz ikonę odświeżania, aby zaktualizować widok.

Kliknij prawym przyciskiem myszy nazwę modułu, aby wyświetlić i edytować bliźniaczej reprezentacji modułu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę przy użyciu kodu programu Visual Studio](how-to-deploy-monitor.md)
