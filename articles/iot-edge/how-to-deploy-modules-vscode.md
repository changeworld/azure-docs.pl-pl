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
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126413"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Wdrażanie usługi Azure IoT Edge modułów z programu Visual Studio Code

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy.

W tym artykule pokazano, jak utworzyć manifest wdrożenia JSON, a następnie użyć do wypychania wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacji o tworzeniu wdrożenia, który jest przeznaczony dla wielu urządzeń, na podstawie ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
* [Urządzenie usługi IoT Edge](how-to-register-device-portal.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla programu Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

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

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

Rozszerzenia usługi Azure IoT dla programu Visual Studio Code służy do wykonywania operacji w Centrum IoT. Dla tych operacji do pracy musisz zalogować się do konta platformy Azure i wybierz Centrum IoT hub, który użytkownik pracuje.

1. W programie Visual Studio Code Otwórz **Explorer** widoku.

1. W dolnej części Eksploratora rozwiń **Azure IoT Hub Devices** sekcji.

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka.

1. Wybierz **wybierz Centrum IoT Hub**.

1. Jeśli nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz Centrum IoT hub.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Możesz wdrożyć moduły do Twojego urządzenia, stosując manifestu wdrażania, który został skonfigurowany z informacjami o module.

1. W widoku Eksploratora Visual Studio Code, rozwiń węzeł **Azure IoT Hub Devices** sekcji.

1. Kliknij prawym przyciskiem myszy na urządzeniu usługi IoT Edge, które chcesz skonfigurować za pomocą pliku manifestu wdrożenia.

    > [!TIP]
    > Aby potwierdzić, że urządzenie zostało wybrane jest urządzenia usługi IoT Edge, wybierz ją, aby rozwinąć listę modułów i sprawdź obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie usługi IoT Edge obejmuje te dwa moduły.

1. Wybierz **tworzenie wdrożenia dla jednego urządzenia**.

1. Przejdź do pliku manifestu wdrożenia JSON, którego chcesz użyć, a następnie kliknij przycisk **wybierz manifestu wdrażania krawędzi**.

   ![Manifest wdrażania wybierz krawędź](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Wyniki swojego wdrożenia są drukowane w danych wyjściowych programu VS Code. Pomyślnych wdrożeniach są stosowane w ciągu kilku minut, jeśli na urządzeniu docelowym jest uruchomiony i połączony z Internetem.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich w **Azure IoT Hub Devices** sekcji. Wybierz strzałkę obok urządzenia usługi IoT Edge, aby ją rozwinąć. Wyświetlane są wszystkie aktualnie uruchomione moduły.

Jeśli ostatnio wdrożono nowe moduły na urządzeniu, umieść kursor nad **Azure IoT Hub Devices** nagłówku sekcji i wybierz ikonę odświeżania, aby zaktualizować widoku.

Kliknij prawym przyciskiem myszy nazwę modułu do wyświetlania i edytowania bliźniaczej reprezentacji modułu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
