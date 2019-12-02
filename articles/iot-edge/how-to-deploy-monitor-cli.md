---
title: Wdrażanie modułów na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure — Azure IoT Edge
description: Tworzenie wdrożeń automatycznych dla grup IoT Edge urządzeń przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 14c4ddd5d95abb223fb30e2ce07496e7f2773257
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666022"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie modułów IoT Edge na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz **IoT Edge Automatyczne wdrażanie** przy użyciu interfejsu wiersza polecenia platformy Azure do zarządzania trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby. 

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

W tym artykule opisano konfigurowanie interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak wdrażać moduły na zestawie IoT Edge urządzeń i monitorować postęp przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="cli-prerequisites"></a>Wymagania wstępne interfejsu wiersza polecenia

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure. 
* [IoT Edge urządzeń](how-to-register-device.md#prerequisites-for-the-azure-cli) z zainstalowanym IoT Edge środowiska uruchomieniowego.
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.24 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, Zapisz manifest wdrożenia lokalnie jako plik txt. Ścieżka do pliku jest używana w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację na urządzeniu. 

Oto podstawowy manifest wdrożenia z jednym modułem:

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
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
}
```

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy mieć możliwość określania, które urządzenia mają mieć wpływ. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania. Na przykład w przypadku zarządzania najwyższego budynku można dodać następujące znaczniki do urządzenia:

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

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Wdrażasz moduły na urządzeniach docelowych, tworząc wdrożenie, które składa się z manifestu wdrożenia, a także innych parametrów. 

Użyj polecenia [AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) , aby utworzyć wdrożenie:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Polecenie wdrożenia Create przyjmuje następujące parametry: 

* **--Deployment-ID** — nazwa wdrożenia, które zostanie utworzone w usłudze IoT Hub. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /`.
* **--Hub-Name** -Name Centrum IoT, w którym zostanie utworzone wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Zmień bieżącą subskrypcję za pomocą polecenia `az account set -s [subscription name]`.
* **--Content** -FilePath do pliku JSON manifestu wdrożenia. 
* **--etykiety** — Dodawanie etykiet w celu ułatwienia śledzenia wdrożeń. Etykiety to nazwy i pary wartości opisujące wdrożenie. Etykiety przyjmują formatowanie JSON dla nazw i wartości. Na przykład: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--Target-Condition** -wprowadź warunek docelowy, aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--Priority** -dodatnia liczba całkowita. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, stosowane będzie wdrożenie o najwyższej wartości liczbowej dla priorytetu.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Użyj polecenia [AZ IoT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) , aby wyświetlić szczegółowe informacje o pojedynczym wdrożeniu:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie Deployment show przyjmuje następujące parametry:
* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź wdrożenie w oknie poleceń. Właściwość **metryki** zawiera liczbę dla każdej metryki ocenianej przez poszczególne centra:

* **targetedCount** — Metryka systemowa, która określa liczbę bliźniaczych reprezentacji urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej.
* **appliedCount** — Metryka systemowa określa liczbę urządzeń, których zawartość wdrożenia została zastosowana do modułu bliźniaczych reprezentacji w IoT Hub.
* **reportedSuccessfulCount** — Metryka urządzenia, która określa liczbę IoT Edge urządzeń w ramach raportowania wdrożenia zakończonych powodzeniem z IoT Edge środowiska uruchomieniowego klienta.
* **reportedFailedCount** — Metryka urządzenia, która określa liczbę IoT Edge urządzeń w przypadku niepowodzenia raportowania wdrożenia w środowisku uruchomieniowym klienta IoT Edge.

Listę identyfikatorów urządzeń lub obiektów dla każdej z metryk można wyświetlić za pomocą polecenia [AZ IoT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Polecenie show Deployment-Metric pobiera następujące parametry: 
* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Metric-ID** -Nazwa metryki, dla której ma zostać wyświetlona lista identyfikatorów urządzeń, na przykład `reportedFailedCount`
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą. 

W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia warunki nowego elementu docelowego, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane na urządzeniu. 
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego, Odinstalowuje to wdrożenie i podejmuje kolejne wdrożenie o najwyższym priorytecie. 
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, nie nastąpi żadne zmiany na urządzeniu. Urządzenie kontynuuje uruchamianie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Po spełnieniu warunku docelowego dowolnego innego wdrożenia Odinstalowuje to wdrożenie i przyjmuje nowe. 

Użyj polecenia [AZ IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) , aby zaktualizować wdrożenie:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Polecenie aktualizacji wdrożenia przyjmuje następujące parametry:
* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`
* **--Set** -aktualizuje właściwość we wdrożeniu. Można zaktualizować następujące właściwości:
  * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
  * Etykieta 
  * priority


## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia. 

Użyj polecenia [AZ IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) , aby usunąć wdrożenie:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Polecenie usunięcia wdrożenia przyjmuje następujące parametry: 
* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
