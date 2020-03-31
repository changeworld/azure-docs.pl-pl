---
title: Wdrażanie modułów na dużą skalę przy użyciu kodu programu Visual Studio — usługa Azure IoT Edge
description: Użyj rozszerzenia IoT dla programu Visual Studio Code, aby utworzyć automatyczne wdrożenia dla grup urządzeń usługi IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774135"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Wdrażanie modułów usługi IoT Edge na dużą skalę przy użyciu kodu programu Visual Studio

Można utworzyć **automatyczne wdrożenie usługi IoT Edge** przy użyciu programu Visual Studio Code do zarządzania bieżącymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia usługi IoT Edge są częścią funkcji [automatycznego zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) usługi IoT Hub. Wdrożenia są procesami dynamicznymi, które umożliwiają wdrażanie wielu modułów na wielu urządzeniach. Można również śledzić stan i kondycję modułów i wprowadzać zmiany, gdy jest to konieczne.

Aby uzyskać więcej informacji, zobacz [Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę.](module-deployment-monitoring.md)

W tym artykule skonfigurować Visual Studio Code i rozszerzenie IoT. Następnie dowiesz się, jak wdrożyć moduły w zestawie urządzeń IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-with-visual-studio-code) z zainstalowanym czasem wykonywania IoT Edge.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla programu Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

Rozszerzenia IoT usługi Azure dla programu Visual Studio Code umożliwiają operacje za pomocą usługi Hub. Aby te operacje działały, musisz zalogować się do konta platformy Azure i wybrać centrum IoT, nad którymi pracujesz.

1. W programie Visual Studio Code otwórz widok **Eksploratora.**

1. U dołu Eksploratora rozwiń sekcję **Centrum Usługi Azure IoT.**

1. Kliknij **...** w nagłówku sekcji **Usługi Azure IoT Hub.** Jeśli wielokropek nie jest widoczny, umieść wskaźnik myszy na nagłówku.

1. Wybierz **pozycję Wybierz Centrum IoT**.

1. Jeśli nie jesteś zalogowany do konta platformy Azure, postępuj zgodnie z instrukcjami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz centrum IoT.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrażania jest dokumentem JSON, który opisuje, które moduły do wdrożenia. Opisano w nim również sposób przepływu danych między modułami i żądane właściwości bliźniacze modułu. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły i ustalać trasy w uliczce IoT.](module-composition.md)

Aby wdrożyć moduły przy użyciu programu Visual Studio Code, zapisz manifest wdrożenia lokalnie jako plik . JSON. Należy podać jego lokalizację po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

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

Jeśli chcesz określić, które urządzenia IoT Edge można obecnie skonfigurować, uruchom polecenie **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identyfikowanie urządzeń z warunkami docelowymi

Aby zidentyfikować urządzenia usługi IoT Edge, które mają odbierać wdrożenie, należy określić warunek docelowy. Warunek docelowy jest spełniony, gdy określone kryteria są zgodne przez deviceId, wartość znacznika lub zgłoszoną wartość właściwości.

Skonfiguruj tagi w bliźniaczej reprezentacji urządzenia. Oto przykład bliźniaczej reprezentacji urządzenia, która ma tagi:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

To urządzenie otrzyma wdrożenie, jeśli warunek docelowy wdrożenia zawiera wyrażenie zgodne z jedną `tag.location.building = '20'`z wartości tagu, taką jak .

Jeśli chcesz kierować określone urządzenie niezależnie od jego tagów lub `deviceId` innych wartości, po prostu określ warunek docelowy.

Oto kilka przykładów:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' LUB deviceId = 'linuxprod2' LUB deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' A NOT deviceId = 'linuxprod1'

Szczegółowe informacje można znaleźć [w warunku docelowym.](module-deployment-monitoring.md#target-condition) Aby uzyskać więcej informacji na temat bliźniąt bliźniąt urządzeń i tagów, zobacz [Opis bliźniaczych urządzeń i używanie ich w U.](../iot-hub/iot-hub-devguide-device-twins.md)

### <a name="edit-the-device-twin"></a>Edytowanie bliźniaczej reprezentacji urządzenia

Można edytować bliźniaczej reprezentacji urządzenia w programie Visual Studio Code, aby skonfigurować tagi. Z menu **Widok** wybierz pozycję **Paleta poleceń** i uruchom polecenie **IoT Edge: Edit Device Twin.** Wybierz urządzenie IoT Edge i pojawi się bliźniacza reprezentacja urządzenia.

W tym przykładzie nie zdefiniowano żadnych tagów. Zastąp `"tags": {}` bieżącą pustą sekcję własną definicją znaczników.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Po zapisaniu pliku lokalnego uruchom polecenie **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Tworzenie wdrożenia na dużą skalę

Po skonfigurowaniu manifestu wdrażania i skonfigurowanych tagów w bliźniaczej reprezentacji urządzenia można przystąpić do wdrożenia.

1. Z menu **Widok** wybierz **pozycję Paleta poleceń** i wybierz polecenie **Azure IoT Edge: Create Deployment at Scale.**

1. Przejdź do pliku JSON manifestu wdrożenia, którego chcesz użyć, a następnie kliknij przycisk **Wybierz manifest wdrożenia krawędzi**.

1. Podaj wartości zgodnie z monitem, zaczynając od **identyfikatora wdrożenia**.

   ![Określanie identyfikatora wdrożenia](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Określ wartości dla tych parametrów:

  | Parametr | Opis |
  | --- | --- |
  | Identyfikator wdrożenia | Nazwa wdrożenia, które zostanie utworzone w centrum IoT Hub. Nadaj wdrożeniu unikatową nazwę, która wynosi do 128 małych liter. Unikaj spacji i następujących `& ^ [ ] { } \ | " < > /`nieprawidłowych znaków: . |
  | Warunek docelowy | Wprowadź warunek docelowy, aby określić, które urządzenia będą kierowane za pomocą tego wdrożenia.Warunek jest oparty na tagach bliźniaczej reprezentacji urządzenia lub bliźniaczej reprezentacji urządzenia zgłoszonych właściwości i powinien być zgodny z formatem wyrażenia.Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Priorytet |  Dodatnia wartość całkowita. Jeśli dwa lub więcej wdrożeń są przeznaczone dla tego samego urządzenia, wdrożenie o najwyższej wartości liczbowej dla priorytetu będzie stosowane. |

  Po określeniu priorytetu terminal powinien wyświetlać wyjście podobne do następującego obrazu:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorowanie i modyfikowanie wdrożeń

Użyj [interfejsu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md#monitor-a-deployment) lub [witryny Azure portal](how-to-deploy-monitor.md#monitor-a-deployment) do monitorowania, modyfikowania i usuwania wdrożeń. Oba zapewniają metryki dotyczące wdrożeń.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na urządzeniach IoT Edge](module-deployment-monitoring.md).
