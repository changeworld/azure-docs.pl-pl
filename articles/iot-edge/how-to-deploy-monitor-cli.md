---
title: Tworzenie wdrożeń w automatycznych z wiersza polecenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie automatycznego wdrażania dla grup usługi IoT Edge urządzenia za pomocą rozszerzenia IoT dla wiersza polecenia platformy Azure
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f93d9eaefe18dd012a639cd26636b56b9eb09249
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595148"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Usługa Azure IoT Edge pozwala na przenoszenie analytics na urządzeniach brzegowych i zapewnia interfejs chmurowy dzięki czemu możesz zarządzać i zdalne monitorowanie urządzeń usługi IoT Edge. Możliwość zdalnego zarządzania urządzeniami jest coraz ważniejsze rozwiązania Internetu rzeczy są coraz większej i bardziej złożonej. Usługa Azure IoT Edge jest przeznaczony do obsługi do celów biznesowych, niezależnie od tego, ile urządzeń, możesz dodać.

Możesz zarządzać poszczególnych urządzeń i wdrażać moduły do nich pojedynczo. Jednak jeśli chcesz wprowadzić zmiany do urządzeń na dużą skalę, możesz utworzyć **automatycznego wdrażania usługi IoT Edge**, który jest częścią automatyczne zarządzanie urządzeniami w usłudze IoT Hub. Wdrożenia są dynamiczne procesów, które umożliwiają jednocześnie wdrażać wielu modułów na wielu urządzeniach, śledzenia stanu i kondycji modułów i wprowadzić zmiany, gdy jest to konieczne. 

W tym artykule, możesz skonfigurować wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak wdrażać moduły do zestawu urządzeń usługi IoT Edge i monitorować postęp za pomocą dostępnych poleceń interfejsu wiersza polecenia.

## <a name="cli-prerequisites"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* [Usługi IoT hub](../iot-hub/iot-hub-create-using-cli.md) w subskrypcji platformy Azure. 
* [Urządzenia usługi IoT Edge](how-to-register-device-cli.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

Aby wdrożyć moduły przy użyciu wiersza polecenia platformy Azure, należy zapisać manifestu wdrażania lokalnie jako plik txt. Ścieżka pliku zostaną użyte w następnej sekcji, po uruchomieniu polecenia, aby zastosować konfigurację do Twojego urządzenia. 

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
   }
   ```

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń za pomocą tagów

Przed utworzeniem wdrożenia, musisz mieć możliwość określenia urządzeń, które mają wpływ na. Usługa Azure IoT Edge identyfikuje urządzenia przy użyciu **tagi** w bliźniaczej reprezentacji urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla Twojego rozwiązania. Na przykład jeśli zarządzasz campus budynki, możesz dodać następujące znaczniki na urządzeniu:

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

Aby uzyskać więcej informacji na temat tagów i bliźniacze reprezentacje urządzeń, zobacz [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Moduły jest wdrożyć na urządzeniach docelowych, tworząc wdrożenia, który składa się z manifestu wdrożenia, a także inne parametry. 

Aby utworzyć wdrożenie, użyj następującego polecenia:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który zostanie utworzony w usłudze IoT hub. Nadaj wdrożenia unikatową nazwę, która jest maksymalnie 128 małe litery. Należy unikać miejsca do magazynowania i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
* **— Nazwa centrum** — nazwy Centrum IoT, w której zostanie utworzone wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji przy użyciu polecenia `az account set -s [subscription name]`.
* **--zawartości** -Filepath do wdrożenia manifestu JSON. 
* **--etykiety** — Dodaj etykiety służące do śledzenia wdrożenia. Etykiety są nazwę i pary wartości, które opisują wdrożenia. Etykiety potrwać, formatowanie nazwy i wartości JSON. Na przykład: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--warunek docelowy** -Podaj warunek docelowy, aby ustalić, urządzeń, które zostaną objęte tego wdrożenia. Warunek opiera się na tagów bliźniaczych reprezentacji urządzeń lub zgłoszonych właściwości bliźniaczej reprezentacji urządzenia, a powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priorytet** -dodatnią liczbą całkowitą. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, będą stosowane wdrożenie o najwyższej wartości liczbowe dla priorytetu.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Użyj następującego polecenia, aby wyświetlić zawartość wdrożenia:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w usłudze IoT hub.
* **— Nazwa centrum** — nazwy Centrum IoT, w której istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź, czy wdrożenie w oknie wiersza polecenia. **Metryki** listy właściwości — liczba, dla każdego metryki, które jest obliczane przez każdego Centrum:

* **targetedCount** — metryki systemu, która określa liczbę bliźniaczych reprezentacji urządzeń w usłudze IoT Hub odpowiada warunkowi określania wartości docelowej.
* **appliedCount** — metryki systemu określa liczbę urządzeń, które były zawartości wdrożenia dotyczą ich bliźniaczych reprezentacjach modułów usługi IoT Hub.
* **reportedSuccessfulCount** — metryki urządzenia, która określa liczbę urządzeń brzegowych we wdrożeniu zgłaszanie powodzenia ze środowiska wykonawczego klienta usługi IoT Edge.
* **reportedFailedCount** — metryki urządzenia, która określa liczbę urządzeń brzegowych we wdrożeniu, raportowania awarii ze środowiska wykonawczego klienta usługi IoT Edge.

Możesz wyświetlić listę identyfikatorów urządzeń lub obiektów dla każdego z metryk, za pomocą następującego polecenia:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w usłudze IoT hub.
* **— identyfikator metryki** — Nazwa metryki, dla którego chcesz wyświetlić listę urządzeń identyfikatorów, na przykład `reportedFailedCount`
* **— Nazwa centrum** — nazwy Centrum IoT, w której istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Zmodyfikuj wdrożenie

Podczas modyfikowania wdrożenia zmiany są natychmiast replikowane do wszystkie objęte nimi urządzenia. 

Jeśli zaktualizujesz warunek docelowy, zachodzą następujące aktualizacje:

* Jeśli urządzenie nie spełnia warunek docelowy stare, ale nowy warunek docelowy spełnia, to wdrożenie ma najwyższy priorytet dla tego urządzenia to wdrożenie jest zastosowany na urządzeniu. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, odinstalowuje tego wdrożenia i Trwa dalej wdrożenia najwyższy priorytet. 
* Jeśli urządzenie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy wszystkich innych wdrożeń, żadna zmiana występuje na urządzeniu. Urządzenie będzie nadal działać jego bieżący modułów w ich bieżący stan, ale nie jest zarządzany w ramach tego wdrożenia już. Gdy spełnia warunek docelowy wszystkich innych wdrożeń, odinstalowuje tego wdrożenia i przejście na nowy. 

Użyj następującego polecenia do zaktualizowania wdrożenia:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w usłudze IoT hub.
* **— Nazwa centrum** — nazwy Centrum IoT, w której istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`
* **— Ustaw** — tak zaktualizować odnośną właściwość we wdrożeniu. Można aktualizować następujące właściwości:
  * targetCondition — przykład `targetCondition=tags.location.state='Oregon'`
  * etykiety 
  * priority


## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przyjmują ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy wszystkich innych wdrożeń, następnie modułów nie są usuwane po usunięciu wdrożenia. 

Aby usunąć wdrożenie, użyj następującego polecenia:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w usłudze IoT hub.
* **— Nazwa centrum** — nazwy Centrum IoT, w której istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do odpowiedniej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Wdrażanie modułów na urządzenia brzegowe](module-deployment-monitoring.md).
