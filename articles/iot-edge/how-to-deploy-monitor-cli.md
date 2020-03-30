---
title: Wdrażanie modułów na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure — usługa Azure IoT Edge
description: Użyj rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, aby utworzyć automatyczne wdrożenia dla grup urządzeń usługi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271475"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie modułów usługi IoT Edge na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz **automatyczne wdrożenie usługi IoT Edge** przy użyciu interfejsu wiersza polecenia platformy Azure do zarządzania bieżącymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia usługi IoT Edge są częścią funkcji [automatycznego zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) usługi IoT Hub. Wdrożenia są procesami dynamicznymi, które umożliwiają wdrażanie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę.](module-deployment-monitoring.md)

W tym artykule skonfigurować interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak wdrożyć moduły w zestawie urządzeń usługi IoT Edge i monitorować postęp przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="cli-prerequisites"></a>Wymagania wstępne interfejsu wiersza polecenia

* [Centrum IoT w](../iot-hub/iot-hub-create-using-cli.md) subskrypcji platformy Azure.
* [Urządzenia IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) z zainstalowanym czasem pracy IoT Edge.
* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w twoim środowisku. Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły i ustalać trasy w uliczce IoT.](module-composition.md)

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, zapisz manifest wdrożenia lokalnie jako plik txt. Ścieżka pliku w następnej sekcji jest używana po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

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

## <a name="layered-deployment"></a>Wdrażanie warstwowe

Wdrożenia warstwowe są rodzajem wdrożenia automatycznego, które można układać jeden na drugim. Aby uzyskać więcej informacji na temat wdrożeń warstwowych, zobacz [Opis automatycznych wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę](module-deployment-monitoring.md).

Wielowarstwowe wdrożenia można tworzyć i zarządzać za pomocą interfejsu wiersza polecenia platformy Azure, jak każde automatyczne wdrożenie, z zaledwie kilkoma różnicami. Po utworzeniu wdrożenia warstwowego ta sama praca interfejsu wiersza polecenia platformy Azure dla wdrożeń warstwowych jest taka sama jak w przypadku każdego wdrożenia. Aby utworzyć wdrożenie warstwowe, `--layered` dodaj flagę do polecenia utwórz.

Druga różnica dotyczy budowy manifestu wdrażania. Podczas standardowego automatycznego wdrażania musi zawierać moduły środowiska uruchomieniowego systemu oprócz wszystkich modułów użytkownika, wielowarstwowe wdrożenia mogą zawierać tylko moduły użytkownika. Zamiast tego wdrożenia warstwowe wymagają standardowego wdrożenia automatycznego również na urządzeniu, aby dostarczyć wymagane składniki każdego urządzenia usługi IoT Edge, takie jak moduły środowiska wykonawczego systemu.

Oto podstawowy manifest wdrażania warstwowego z jednym modułem jako przykład:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

W poprzednim przykładzie pokazano ustawienia `properties.desired` wdrożenia warstwowego dla modułu. Jeśli to wdrożenie warstwowe ukierunkowane na urządzenie, na którym ten sam moduł został już zastosowany, zastąpiłoby wszystkie istniejące żądane właściwości. Aby zaktualizować, zamiast zastępować żądane właściwości, można zdefiniować nową podsekcję. Przykład:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Aby uzyskać więcej informacji na temat konfigurowania bliźniaczek modułów bliźniaczek we wdrożeniach warstwowych, zobacz [Wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu znaczników

Przed utworzeniem wdrożenia należy określić, na które urządzenia mają mieć wpływ. Usługa Azure IoT Edge identyfikuje urządzenia przy użyciu tagów w bliźniaczej **reprezentacji** urządzenia. Każde urządzenie może mieć wiele tagów, które można zdefiniować w dowolny sposób, który ma sens dla rozwiązania. Jeśli na przykład zarządzasz kampusem inteligentnych budynków, możesz dodać do urządzenia następujące znaczniki:

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

Aby uzyskać więcej informacji na temat bliźniąt bliźniąt urządzeń i tagów, zobacz [Opis bliźniaczych urządzeń i używanie ich w U.](../iot-hub/iot-hub-devguide-device-twins.md)

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Moduły można wdrożyć na urządzeniach docelowych, tworząc wdrożenie, które składa się z manifestu wdrażania, a także innych parametrów.

Użyj polecenia [tworzenia wdrożenia az iot edge,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) aby utworzyć wdrożenie:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Użyj tego samego `--layered` polecenia z flagą, aby utworzyć warstwowe deploymet.

Polecenie utwórz wdrożenie przyjmuje następujące parametry:

* **--layered** — opcjonalna flaga identyfikująca wdrożenie jako wdrożenie warstwowe.
* **--deployment-id** — nazwa wdrożenia, które zostanie utworzone w centrum IoT Hub. Nadaj wdrożeniu unikatową nazwę, która wynosi do 128 małych liter. Unikaj spacji i następujących `& ^ [ ] { } \ | " < > /`nieprawidłowych znaków: . Parametr wymagany.
* **--content** — ścieżka pliku do manifestu wdrażania JSON. Parametr wymagany.
* **--hub-name** — nazwa centrum IoT hub, w którym zostanie utworzone wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Zmień bieżącą subskrypcję za `az account set -s [subscription name]` pomocą polecenia.
* **--labels** — dodaj etykiety, aby ułatwić śledzenie wdrożeń. Etykiety to Name, Value par, które opisują wdrożenia. Etykiety przyjmują formatowanie JSON dla nazw i wartości. Na przykład: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** — wprowadź warunek docelowy, aby określić, które urządzenia będą kierowane za pomocą tego wdrożenia.Warunek jest oparty na tagach bliźniaczej reprezentacji urządzenia lub bliźniaczej reprezentacji urządzenia zgłoszonych właściwości i powinien być zgodny z formatem wyrażenia.Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** — dodatnia ć całkowita. W przypadku, gdy dwa lub więcej wdrożeń są przeznaczone dla tego samego urządzenia, wdrożenie o najwyższej wartości liczbowej dla priorytetu będzie stosowane.
* **--metryki** — tworzenie metryk, które kwerendy edgeHub zgłaszane właściwości do śledzenia stanu wdrożenia. Metryki wziąć wejście JSON lub ścieżkę pliku. Na przykład `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Użyj polecenia [az iot edge deployment show,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) aby wyświetlić szczegóły pojedynczego wdrożenia:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie show wdrażania przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT Hub. Parametr wymagany.
* **--hub-name** — nazwa centrum IoT hub, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełączanie do żądanej subskrypcji za pomocą polecenia`az account set -s [subscription name]`

Sprawdź wdrożenie w oknie polecenia.Właściwość **metryki** zawiera listę licznika dla każdej metryki, która jest oceniana przez każdego koncentratora:

* **targetedCount** - metryka systemu określająca liczbę bliźniaczych urządzeń w Centrum IoT, które odpowiadają warunkowi kierowania.
* **appliedCount** — metryka systemu określa liczbę urządzeń, które miały zawartość wdrożenia stosowane do ich bliźniaczych modułów w U. IoT Hub.
* **reportedSuccessfulCount** — metryka urządzenia określająca liczbę urządzeń usługi IoT Edge w wyniku raportowania wdrożenia ze środowiska wykonawczego klienta usługi IoT Edge.
* **reportedFailedCount** — metryka urządzenia określająca liczbę urządzeń usługi IoT Edge w przypadku niepowodzenia raportowania wdrożenia ze środowiska wykonawczego klienta usługi IoT Edge.

Można wyświetlić listę identyfikatorów urządzeń lub obiektów dla każdej z metryk za pomocą polecenia [az iot edge deployment show-metric:](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Polecenie show-metryka wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT Hub.
* **--metric-id** — nazwa metryki, dla której ma zostać wyświetlona lista `reportedFailedCount`identyfikatorów urządzeń, na przykład .
* **--hub-name** — nazwa centrum IoT hub, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Podczas modyfikowania wdrożenia zmiany natychmiast replikują się na wszystkie urządzenia docelowe.

W przypadku aktualizacji warunku docelowego wystąpią następujące aktualizacje:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia nowy warunek docelowy i to wdrożenie jest najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane do urządzenia.
* Jeśli urządzenie aktualnie uruchomione to wdrożenie nie spełnia już warunku docelowego, odinstalowuje to wdrożenie i przyjmuje następne wdrożenie o najwyższym priorytecie.
* Jeśli urządzenie aktualnie uruchomione to wdrożenie nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, na urządzeniu nie nastąpi żadna zmiana. Urządzenie kontynuuje uruchamianie swoich bieżących modułów w ich bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Gdy spełnia warunek docelowy innego wdrożenia, odinstalowuje to wdrożenie i przyjmuje nowe.

Nie można zaktualizować zawartość wdrożenia, który zawiera moduły i trasy zdefiniowane w manifeście wdrażania. Jeśli chcesz zaktualizować zawartość wdrożenia, należy to zrobić, tworząc nowe wdrożenie, które jest przeznaczone dla tych samych urządzeń o wyższym priorytecie. Można zmodyfikować niektóre właściwości istniejącego modułu, w tym warunek docelowy, etykiety, metryki i priorytet.

Użyj polecenia [aktualizacji wdrożenia az iot edge,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) aby zaktualizować wdrożenie:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Polecenie aktualizacji wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT Hub.
* **--hub-name** — nazwa centrum IoT hub, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełączanie do żądanej subskrypcji za pomocą polecenia`az account set -s [subscription name]`
* **--set** — aktualizowanie właściwości we wdrożeniu. Można zaktualizować następujące właściwości:
  * targetCondition - na przykład`targetCondition=tags.location.state='Oregon'`
  * Etykiety
  * priority
* **--add** - Dodaj nową właściwość do wdrożenia, w tym warunki docelowe lub etykiety.
* **--remove** - Usuń istniejącą właściwość, w tym warunki docelowe lub etykiety.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie urządzenia przyjmą następne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie są usuwane po usunięciu wdrożenia.

Użyj polecenia [usuwania wdrożenia az iot edge,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) aby usunąć wdrożenie:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie delete wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT Hub.
* **--hub-name** — nazwa centrum IoT hub, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełączanie do żądanej subskrypcji za pomocą polecenia`az account set -s [subscription name]`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na urządzeniach IoT Edge](module-deployment-monitoring.md).
