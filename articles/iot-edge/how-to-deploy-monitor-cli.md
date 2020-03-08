---
title: Wdrażanie modułów na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure — Azure IoT Edge
description: Tworzenie automatycznego wdrażania dla grup usługi IoT Edge urządzenia za pomocą rozszerzenia IoT dla wiersza polecenia platformy Azure
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893088"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę przy użyciu wiersza polecenia platformy Azure

Utwórz **IoT Edge Automatyczne wdrażanie** przy użyciu interfejsu wiersza polecenia platformy Azure do zarządzania trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

W tym artykule, możesz skonfigurować wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak wdrażać moduły na zestawie IoT Edge urządzeń i monitorować postęp przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="cli-prerequisites"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [IoT Edge urządzeń](how-to-register-device.md#prerequisites-for-the-azure-cli) z zainstalowanym IoT Edge środowiska uruchomieniowego.
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu wiersza polecenia platformy Azure, należy zapisać manifestu wdrażania lokalnie jako plik txt. Ścieżka do pliku jest używana w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację na urządzeniu.

Poniżej przedstawiono manifestu podstawowego wdrożenia za pomocą jednego modułu, na przykład:

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

## <a name="layered-deployment"></a>Wdrożenie warstwowe

Wdrożenia warstwowe są typem automatycznego wdrażania, które mogą być ułożone między sobą. Aby uzyskać więcej informacji na temat wdrożeń warstwowych, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Wdrożenia warstwowe można tworzyć i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure, takiego jak dowolne wdrożenie automatyczne, za pomocą zaledwie kilku różnic. Po utworzeniu wdrożenia warstwowego ten sam interfejs wiersza polecenia platformy Azure działa w przypadku wdrożeń warstwowych w taki sam sposób, jak w przypadku każdego wdrożenia. Aby utworzyć wdrożenie warstwowe, Dodaj flagę `--layered` do polecenia CREATE.

Druga różnica polega na przygotowaniu manifestu wdrożenia. Chociaż standardowe wdrożenie automatyczne musi zawierać moduły środowiska uruchomieniowego systemu oprócz modułów użytkownika, wdrożenia warstwowe mogą zawierać tylko moduły użytkownika. Zamiast tego wdrożenia warstwowe wymagają standardowego wdrożenia automatycznego również na urządzeniu, aby dostarczyć wymagane składniki każdego IoT Edge urządzenia, takie jak moduły środowiska uruchomieniowego systemu.

Poniżej przedstawiono podstawowy manifest wdrożenia warstwowego z jednym modułem na przykład:

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

W poprzednim przykładzie pokazano wdrożenie warstwowe `properties.desired` dla modułu. Jeśli to wdrożenie warstwowe nadano urządzeniu, w którym już zastosowano ten sam moduł, spowoduje to zastąpienie wszelkich istniejących żądanych właściwości. Aby można było zaktualizować zamiast zastępować odpowiednie właściwości, możesz zdefiniować nową podsekcję. Na przykład:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Aby uzyskać więcej informacji o konfigurowaniu modułu bliźniaczych reprezentacji w przypadku wdrożeń warstwowych, zobacz [wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed utworzeniem wdrożenia, musisz mieć możliwość określenia urządzeń, które mają wpływ na. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania. Na przykład jeśli zarządzasz campus budynki, możesz dodać następujące znaczniki na urządzeniu:

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

Moduły jest wdrożyć na urządzeniach docelowych, tworząc wdrożenia, który składa się z manifestu wdrożenia, a także inne parametry.

Użyj polecenia [AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) , aby utworzyć wdrożenie:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Użyj tego samego polecenia z flagą `--layered`, aby utworzyć deploymet warstwowe.

Polecenie wdrożenia Create przyjmuje następujące parametry:

* **--warstwowy** — opcjonalna flaga identyfikująca wdrożenie jako wdrożenie warstwowe.
* **--Deployment-ID** — nazwa wdrożenia, które zostanie utworzone w usłudze IoT Hub. Nadaj wdrożenia unikatową nazwę, która jest maksymalnie 128 małe litery. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /`. Parametr wymagany.
* **--Content** -FilePath do pliku JSON manifestu wdrożenia. Parametr wymagany.
* **--Hub-Name** -Name Centrum IoT, w którym zostanie utworzone wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Zmień bieżącą subskrypcję za pomocą polecenia `az account set -s [subscription name]`.
* **--etykiety** — Dodawanie etykiet w celu ułatwienia śledzenia wdrożeń. Etykiety są nazwę i pary wartości, które opisują wdrożenia. Etykiety przyjmują formatowanie JSON dla nazw i wartości. Na przykład: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--Target-Condition** -wprowadź warunek docelowy, aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--Priority** -dodatnia liczba całkowita. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, będą stosowane wdrożenie o najwyższej wartości liczbowe dla priorytetu.
* **--Metrics** — Utwórz metryki, które wysyłają zapytania do edgeHub raportowanych właściwości, aby śledzić stan wdrożenia. Metryki pobierają dane wejściowe JSON lub ścieżki. Na przykład `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Użyj polecenia [AZ IoT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) , aby wyświetlić szczegółowe informacje o pojedynczym wdrożeniu:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie Deployment show przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub. Parametr wymagany.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź, czy wdrożenie w oknie wiersza polecenia. Właściwość **metryki** zawiera liczbę dla każdej metryki ocenianej przez poszczególne centra:

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
* **--Metric-ID** -Nazwa metryki, dla której ma zostać wyświetlona lista identyfikatorów urządzeń, na przykład `reportedFailedCount`.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Zmodyfikuj wdrożenie

Podczas modyfikowania wdrożenia zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia.

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli urządzenie nie spełnia warunek docelowy stare, ale nowy warunek docelowy spełnia, to wdrożenie ma najwyższy priorytet dla tego urządzenia to wdrożenie jest zastosowany na urządzeniu.
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, odinstalowuje tego wdrożenia i Trwa dalej wdrożenia najwyższy priorytet.
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy wszystkich innych wdrożeń, żadna zmiana występuje na urządzeniu. Urządzenie będzie nadal działać jego bieżący modułów w ich bieżący stan, ale nie jest zarządzany w ramach tego wdrożenia już. Gdy spełnia warunek docelowy wszystkich innych wdrożeń, odinstalowuje tego wdrożenia i przejście na nowy.

Nie można zaktualizować zawartości wdrożenia, w tym modułów i tras zdefiniowanych w manifeście wdrożenia. Aby zaktualizować zawartość wdrożenia, należy utworzyć nowe wdrożenie, które jest przeznaczone dla tych samych urządzeń o wyższym priorytecie. Można modyfikować pewne właściwości istniejącego modułu, w tym warunek docelowy, etykiety, metryki i priorytet.

Użyj polecenia [AZ IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) , aby zaktualizować wdrożenie:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Polecenie aktualizacji wdrożenia przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`
* **--Set** -aktualizuje właściwość we wdrożeniu. Można aktualizować następujące właściwości:
  * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
  * etykiety
  * priority
* **--Add** -Dodaj nową właściwość do wdrożenia, łącznie z warunkami docelowymi lub etykietami.
* **--Remove** -Usuwa istniejącą właściwość, włącznie z warunkami docelowymi lub etykietami.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przyjmują ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy wszystkich innych wdrożeń, następnie modułów nie są usuwane po usunięciu wdrożenia.

Użyj polecenia [AZ IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) , aby usunąć wdrożenie:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie usunięcia wdrożenia przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
