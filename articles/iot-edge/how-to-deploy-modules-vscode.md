---
title: Wdrażanie modułów z programu Visual Studio Code — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio Code, aby wdrożyć moduły na urządzeniu usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 415494fed0870a855a20314816bcebbe22680a98
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457416"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Wdrażanie usługi Azure IoT Edge modułów z programu Visual Studio Code

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy.

W tym artykule pokazano, jak utworzyć manifest wdrożenia JSON, a następnie użyć do wypychania wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-with-visual-studio-code) z zainstalowanym IoT Edge środowiska uruchomieniowego.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla programu Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu programu Visual Studio Code, Zapisz pliku manifestu wdrożenia lokalnie jako. Plik JSON. Ścieżka pliku zostaną użyte w następnej sekcji, po uruchomieniu polecenia, aby zastosować konfigurację do Twojego urządzenia.

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
               "route": "FROM /* INTO $upstream"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

Rozszerzenia usługi Azure IoT dla programu Visual Studio Code służy do wykonywania operacji w Centrum IoT. Dla tych operacji do pracy musisz zalogować się do konta platformy Azure i wybierz Centrum IoT hub, który użytkownik pracuje.

1. W Visual Studio Code Otwórz widok **Eksploratora** .

1. W dolnej części Eksploratora rozwiń sekcję **usługi Azure IoT Hub Devices** .

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kliknij pozycję **...** w nagłówku sekcji **urządzenia IoT Hub Azure** . Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka.

1. Wybierz **pozycję wybierz IoT Hub**.

1. Jeśli nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz Centrum IoT hub.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Możesz wdrożyć moduły do Twojego urządzenia, stosując manifestu wdrażania, który został skonfigurowany z informacjami o module.

1. W widoku Eksploratora Visual Studio Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** .

1. Kliknij prawym przyciskiem myszy urządzenie IoT Edge, które chcesz skonfigurować przy użyciu manifestu wdrażania.

    > [!TIP]
    > Aby upewnić się, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie IoT Edge obejmuje te dwa moduły.

1. Wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

1. Przejdź do pliku JSON manifestu wdrożenia, którego chcesz użyć, a następnie kliknij pozycję **Wybierz manifest wdrożenia Edge**.

   ![Manifest wdrażania wybierz krawędź](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Wyniki swojego wdrożenia są drukowane w danych wyjściowych programu VS Code. Pomyślnych wdrożeniach są stosowane w ciągu kilku minut, jeśli na urządzeniu docelowym jest uruchomiony i połączony z Internetem.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu można je wyświetlić w sekcji **urządzenia IoT Hub Azure** . Wybierz strzałkę obok urządzenia usługi IoT Edge, aby ją rozwinąć. Wyświetlane są wszystkie aktualnie uruchomione moduły.

Jeśli ostatnio wdrożono nowe moduły na urządzeniu, umieść kursor nad nagłówkiem sekcji **usługi Azure IoT Hub Devices** i wybierz ikonę Odśwież, aby zaktualizować widok.

Kliknij prawym przyciskiem myszy nazwę modułu do wyświetlania i edytowania bliźniaczej reprezentacji modułu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak wdrażać i monitorować moduły IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md)
