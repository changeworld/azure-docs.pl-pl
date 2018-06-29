---
title: Wdrażanie modułów Azure IoT krawędzi (w kodzie VS) | Dokumentacja firmy Microsoft
description: Wdróż modułów na urządzenia IoT za pomocą programu Visual Studio Code
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7e75f2ff5e2df3189683d084a315ad6c8730be84
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036067"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Wdrażanie modułów Azure IoT krawędzi z kodem Visual Studio

Po utworzeniu krawędzi IoT modułów z logiki biznesowej, którą chcesz wdrożyć je na urządzeniach do działania na krawędzi. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, można wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy. 

W tym artykule pokazano, jak utworzyć manifest rozmieszczenia JSON, a następnie użyć do wypychania wdrażanie do urządzenia IoT. Informacji o tworzeniu wdrożenia, które jest przeznaczony dla wielu urządzeń według ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w Twojej subskrypcji platformy Azure. 
* [Urządzenia IoT](how-to-register-device-portal.md) ze środowiskiem uruchomieniowym krawędzi IoT zainstalowane. 
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Rozszerzenie Azure IoT krawędzi](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code. 

## <a name="configure-a-deployment-manifest"></a>Skonfiguruj manifest wdrażania

Manifest rozmieszczenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, jak dane przepływają między modułami i odpowiednie właściwości twins modułu. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia oraz sposób ich tworzenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

Aby wdrożyć modułów za pomocą programu Visual Studio Code, Zapisz manifest rozmieszczenia lokalnie jako. Plik JSON. Ścieżka pliku użyje w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do Centrum IoT

Rozszerzenia Azure IoT dla programu Visual Studio Code służy do wykonywania operacji z Centrum IoT. Do tych operacji do pracy należy zalogować się do konta platformy Azure i wybierz Centrum IoT, z którym pracujesz na.

1. Otwórz w programie Visual Studio Code, **Explorer** widoku.

2. W dolnej części Eksploratora, rozwiń węzeł **urządzeń Centrum IoT Azure** sekcji. 

   ![Rozwiń węzeł urządzenia Centrum IoT Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Polecenie **...**  w **urządzeń Centrum IoT Azure** nagłówek sekcji. Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka. 

4. Wybierz **wybierz Centrum IoT**.

5. Jeśli użytkownik nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić. 

6. Wybierz swoją subskrypcję platformy Azure. 

7. Wybierz Centrum IoT. 


## <a name="deploy-to-your-device"></a>Wdrażanie do urządzenia

Moduły można wdrożyć na urządzeniu, stosując skonfigurowana z informacjami o module manifest wdrażania. 

1. W widoku Eksploratora Visual Studio Code, rozwiń węzeł **urządzeń Centrum IoT Azure** sekcji. 

2. Kliknij prawym przyciskiem myszy na urządzeniu, który chcesz skonfigurować manifest wdrażania. 

3. Wybierz **tworzenie wdrożenia dla urządzenia IoT**. 

4. Przejdź do wdrożenia manifestu pliku JSON, który ma być używany, a następnie kliknij przycisk **wybierz Manifest wdrażania krawędzi**. 

   ![Wybierz Manifest rozmieszczenia krawędzi](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Wyniki wdrożenia są podane w kodzie VS danych wyjściowych. Pomyślnego wdrożenia są stosowane w ciągu kilku minut, jeśli urządzenia docelowego jest uruchomiony i połączony z Internetem. 

## <a name="view-modules-on-your-device"></a>Widok modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu, można wyświetlić wszystkie z nich w **urządzeń Centrum IoT Azure** sekcji. Wybierz strzałkę obok urządzenia IoT krawędzi aby go rozwinąć. Wyświetlane są wszystkie aktualnie uruchomionych modułów. 

Jeśli ostatnio wdrożonych nowych modułów na urządzeniu, umieść kursor nad **urządzeń Centrum IoT Azure** sekcji nagłówka i wybierz ikonę odświeżania, aby zaktualizować widoku. 

Kliknij prawym przyciskiem myszy nazwę modułu, aby wyświetlić i edytować dwie modułu. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md)