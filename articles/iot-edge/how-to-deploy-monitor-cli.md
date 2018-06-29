---
title: Wdrażanie i monitorowanie modułów dla usługi Azure IoT krawędzi (CLI) | Dokumentacja firmy Microsoft
description: Zarządzanie modułów, które działają na urządzeniach krawędzi w
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3dfb0fe0227fdd0ff1a43cb7b0a89eb9d3e066f4
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097941"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę, przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Krawędź IoT Azure umożliwia przenoszenie analytics krawędzi i udostępnia interfejs chmury, dzięki czemu można zarządzać i monitorować urządzenia IoT krawędzi bez konieczności fizycznie dostęp do każdej z nich. Możliwość zdalnego zarządzania urządzeniami jest coraz bardziej istotne, ponieważ Internetu rzeczy rozwiązań rośnie większej i bardziej złożonej. Azure IoT krawędzi służy do celów biznesowych, niezależnie od tego, ile urządzeń, możesz dodać.

Można zarządzać poszczególnych urządzeń i wdrożyć moduły pojedynczo. Jednak jeśli chcesz wprowadzić zmiany na urządzeniach na dużą skalę, możesz utworzyć **wdrażania automatycznego krawędzi IoT**, który jest częścią automatyczne zarządzanie urządzeniami w Centrum IoT. Wdrożenia są dynamiczne procesów, które można wdrożyć wiele modułów jednocześnie na wielu urządzeniach, śledzić stan i kondycję moduły i wprowadzić zmiany, gdy jest to konieczne. 

W tym artykule skonfigurujesz interfejs wiersza polecenia platformy Azure w wersji 2.0 i rozszerzenie IoT. Następnie zostanie przedstawiony sposób wdrażania modułów do zestawu urządzenia brzegowe IoT i monitorować postęp, za pomocą dostępnych poleceń interfejsu wiersza polecenia.

## <a name="cli-prerequisites"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w Twojej subskrypcji platformy Azure. 
* [Urządzenia brzegowe IoT](how-to-register-device-cli.md) ze środowiskiem uruchomieniowym krawędzi IoT zainstalowane.
* Zainstalowany w środowisku [interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Skonfiguruj manifest wdrażania

Manifest rozmieszczenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, jak dane przepływają między modułami i odpowiednie właściwości twins modułu. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia oraz sposób ich tworzenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

Aby wdrożyć modułów za pomocą usługi Azure CLI 2.0, Zapisz lokalnie manifest rozmieszczenia jako plik .txt. Ścieżka pliku użyje w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia. 

Oto manifestu podstawowego wdrożenia z jednego modułu jako przykład:

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


## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia, należy być w stanie określić, które mają wpływ na urządzenia. Azure IoT krawędzi identyfikuje urządzeń przy użyciu **tagi** w dwie urządzenia. Każde urządzenie może mieć wiele tagów i można je zdefiniować sposób, który ma sens dla rozwiązania. Na przykład jeśli zarządzasz firmy inteligentne budynków, można dodać następujących tagów do urządzenia:

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

Aby uzyskać więcej informacji na temat twins urządzenia i tagów, zobacz [opis i użyj twins urządzenie w Centrum IoT][lnk-device-twin].

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Moduły można wdrożyć na urządzenia docelowego, tworząc wdrożenia, która składa się z manifestu wdrożenia, a także inne parametry. 

Aby utworzyć wdrożenie, użyj następującego polecenia:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, które zostaną utworzone w Centrum IoT. Nadaj unikatową nazwę, która jest maksymalnie 128 małe litery wdrożenia. Unikaj spacje i następujące nieprawidłowe znaki: `& ^ [ ] { } \ | " < > /`.
* **--etykiety** — Dodawanie etykiet, aby ułatwić śledzenie wdrożeń. Etykiety są nazwę i pary wartości, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`
* **--zawartości** -JSON manifestu Filepath do wdrożenia. 
* **--nazwy centrum** — nazwa Centrum IoT, w którym będzie można utworzyć wdrożenia. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`
* **--warunek docelowy** -Podaj warunek docelowy określ urządzeń, które będą stosowane w tym wdrożeniu. Warunek jest oparta na tagi dwie urządzenia lub dwie urządzenia żądanego właściwości i powinny być zgodne z formatem wyrażenia. Na przykład `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`. 
* **--priorytet** -dodatnią liczbą całkowitą. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, będą miały zastosowania wdrożenia z najwyższą wartością liczbową priorytetu.

## <a name="monitor-a-deployment"></a>Monitorowanie wdrożenia

Aby wyświetlić zawartość wdrożenia, użyj następującego polecenia:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje wdrożenie. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`

Sprawdź, czy wdrożenie w oknie wiersza polecenia. **Metryki** liczba dla każdej metryki, które jest oceniane w każdej Centrum list właściwości:
* **targetedCount** -metrykę systemu, która określa liczbę twins urządzenie w Centrum IoT, spełniających warunek określania wartości docelowej.
* **appliedCount** -Metryka systemu określa liczbę urządzeń, które były stosowane do ich twins modułu w Centrum IoT wdrożenia zawartości.
* **reportedSuccessfulCount** -metrykę urządzenia, która określa liczbę urządzenia brzegowe we wdrożeniu raportowania Powodzenie ze środowiska uruchomieniowego klienta IoT krawędzi.
* **reportedFailedCount** -metrykę urządzenia, która określa liczbę urządzenia brzegowe we wdrożeniu zgłoszenie błędu na podstawie środowiska uruchomieniowego klienta IoT krawędzi.

Można wyświetlić listę identyfikatory urządzeń lub obiektów dla każdego metryki za pomocą następującego polecenia:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w Centrum IoT.
* **— Identyfikator Metryka** — Nazwa metryki, dla którego chcesz wyświetlić listę urządzeń identyfikatorów, na przykład `reportedFailedCount`
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje wdrożenie. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany natychmiast replikowane do wszystkich urządzeń docelowych. 

Po zaktualizowaniu warunek docelowy są wykonywane następujące aktualizacje:
* Jeśli urządzenie nie spełnia warunek docelowy stare, ale spełnia nowy warunek docelowy i to wdrożenie jest najwyższy priorytet dla tego urządzenia, to wdrożenie jest zastosowany na urządzeniu. 
* Jeśli urządzenie aktualnie uruchomione tego wdrożenia nie spełnia warunek docelowy, odinstalowuje tego wdrożenia i przejmuje dalej wdrożenia najwyższy priorytet. 
* Jeśli urządzenie aktualnie uruchomione tego wdrożenia nie jest już spełnia warunek docelowy, a nie spełnia warunek docelowy innych wdrożeń, zmiany nie występuje na urządzeniu. Urządzenia wciąż trwa jego bieżący modułów w jego bieżącym stanie, ale nie jest zarządzana w ramach już tego wdrożenia. Po spełnia warunek docelowy inne wdrożenia, odinstalowuje tego wdrożenia i przejście na nowy. 

Do zaktualizowania wdrożenia, użyj następującego polecenia:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje wdrożenie. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`
* **--ustawić** — aktualizowanie właściwości we wdrożeniu. Można aktualizować następujące właściwości:
    * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
    * etykiety 
    * priorytet


## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia żadnych urządzeń przełączyć na ich dalej wdrożenia najwyższy priorytet. Jeśli urządzenia nie spełniają warunek docelowy inne wdrożenia, następnie moduły nie zostaną usunięte po usunięciu wdrożenia. 

Aby usunąć wdrożenia, użyj następującego polecenia:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **— Identyfikator wdrożenia** — Nazwa wdrożenia, który istnieje w Centrum IoT.
* **--nazwy centrum** — nazwa Centrum IoT, w którym istnieje wdrożenie. Centrum musi być w bieżącej subskrypcji. Przełącz do żądanego subskrypcji przy użyciu polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wdrożenie modułów urządzenia brzegowe][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
